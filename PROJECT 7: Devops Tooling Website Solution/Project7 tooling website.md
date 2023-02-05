# DEVOPS TOOLING WEBSITE SOLUTION

In the previous project i implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. 

[**Project 6**](https://github.com/iamRichardCN/DEVOPS-PROJECTS/tree/main/Project%206%20Web%20Solution%20With%20WordPress)


Moving further, in this project i will be implementing a tooling website solution which makes access to DevOps tools within the corporate infrastructure easily accessible. The solution implemented will consists of following components:

**Infrastructure**: AWS

**Webserver Linux**: Red Hat Enterprise Linux 8

**Database Server**: Ubuntu 20.04 + MySQL

**Storage Server**: Red Hat Enterprise Linux 8 + NFS Server

**Programming Language:** PHP

**Code Repository:** GitHub


## Architectural Design

![image](https://user-images.githubusercontent.com/111741533/211112407-ef4706bd-6fb2-411e-a598-0cba7f015658.png)

On the diagram above you can see a common pattern where several stateless Web Servers share a common database and also access the same files using Network File Sytem (NFS) as a shared file storage. Even though the NFS server might be located on a completely separate hardware – for Web Servers it look like a local file system from where they can serve the same files.

## Preparing NFS Server

Created an EC2 instance (Red Hat Enterprise Linux 8 on AWS) on which we will setup our NFS(Network File Storage) Server.

On this server we attach three EBS volumes 10GB each as external storage to our instance and create 3 logical volumes on it through which we will attach mounts from our external web servers.

3 logical volumes ```lv-opt```, ```lv-apps``` and ```lv-logs```

3 mount directory ```/mnt/opt```, ```/mnt/apps``` and ```/mnt/logs```

Webserver content will be stores in /apps, webserver logs in /logs and /opt will be used by Jenkins


<img width="806" alt="nfs 3" src="https://user-images.githubusercontent.com/111741533/211551144-408324c5-97b5-42ec-ab1a-04f0aa9c56e9.png">


Installing nfs-server on the nfs instance and ensures that it starts on system reboot


```
sudo yum -y update
sudo yum install nfs-utils -y
sudo systemctl start nfs-server.service
sudo systemctl enable nfs-server.service
sudo systemctl status nfs-server.service
```

After installing the nfs-serve, we set up permission that will allow our Web servers to read, write and execute files on NFS:

```
sudo chown -R nobody: /mnt/apps
sudo chown -R nobody: /mnt/logs
sudo chown -R nobody: /mnt/opt
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```

then restart the server with 

```sudo systemctl restart nfs-server.service```


<img width="821" alt="nfs 7 start nfs to chown" src="https://user-images.githubusercontent.com/111741533/211553081-9212837e-b4e7-4c5c-befb-50b64a36f5e9.png">


we then Exported the mounts for webservers’ subnet cidr to connect as clients. For simplicity, we  installed your all three Web Servers inside the same subnet (but in production set up this is not recommended).

To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

<img width="811" alt="cidr addresss" src="https://user-images.githubusercontent.com/111741533/211554120-556f0a3f-b530-4e76-9a2b-eb2b60c46410.png">


Next we configured NFS to interact with clients present in the same subnet.


```
sudo vi /etc/exports

/mnt/apps 172.31.0.0/16(rw,sync,no_all_squash,no_root_squash)
/mnt/logs 172.31.0.0/16(rw,sync,no_all_squash,no_root_squash)
/mnt/opt 172.31.0.0/16(rw,sync,no_all_squash,no_root_squash)

Esc + :wq!

sudo exportfs -arv
```

To check what port is used by NFS so we can open it in security group


```rpcinfo -p | grep nfs```


<img width="520" alt="nfs 8 export" src="https://user-images.githubusercontent.com/111741533/211560086-93a11006-1313-41d3-8e08-7114904d19e1.png">



The following ports are to be open on the NFS server


<img width="808" alt="security group nfs" src="https://user-images.githubusercontent.com/111741533/211560319-f48fa80d-ad44-4f98-bc2f-becaf8b88fa5.png">



## CONFIGURING THE DATABASE SERVER

we Created an Ubuntu Server on AWS which will serve as our Database. Ensure its in the same subnet as the NFS-Server

Install mysql-server

```
sudo apt -y update
sudo apt install -y mysql-server

#To enter the db environment run
sudo mysql
```

Create a database and name it tooling

Create a database user and name it webaccess

Grant permission to webaccess user on tooling database to do anything only from the webservers ```subnet cidr```


<img width="515" alt="db2" src="https://user-images.githubusercontent.com/111741533/211562455-35fb6de7-ce62-42a1-ab30-d216cfcb3c6a.png">


<img width="619" alt="deb 4 connection confirmation" src="https://user-images.githubusercontent.com/111741533/211562802-b6f9a316-dc35-45df-9706-22e13e28dc5e.png">


## PREPARING THE WEB SERVERS

here, We make sure that our Web Servers can serve the same content from shared storage solutions, in our case – NFS Server and MySQL database

the following configuration is done on  done on the web servers:

- configuring NFS client
- deploying tooling website application
- configure servers to work with database


### Installing NFS-Client

<img width="916" alt="web1 NFS" src="https://user-images.githubusercontent.com/111741533/211564721-d1bbda51-2d2f-49f4-8f0b-641648ac0fbf.png">


Mount /var/www/ and target the NFS server’s export for apps

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```

Verify that NFS was mounted successfully by running ```df -h```. 

Make sure that the changes will persist on Web Server after reboot:

```sudo vi /etc/fstab```

add following line

```<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0```

<img width="746" alt="WEB 2 fstab and etc" src="https://user-images.githubusercontent.com/111741533/211565376-f4451a07-faf0-4dfd-bb1b-4a7494fe6a46.png">


### Installing Remi’s repository, Apache and PHP

```
sudo yum install httpd -y
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo dnf module reset php
sudo dnf module enable php:remi-7.4
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```


Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs.

<img width="921" alt="web 3" src="https://user-images.githubusercontent.com/111741533/211566776-180c63c1-3960-4d8f-8533-efddfed09021.png">


Install git to Fork the tooling source code from [Darey.io](https://github.com/darey-io/tooling.git)


<img width="658" alt="web 4 git to apache" src="https://user-images.githubusercontent.com/111741533/211567393-c47c3838-afa4-4e2b-8d62-be21f40b9a1f.png">


Note 1: Do not forget to open TCP port 80 on the Web Server.


Login via web broswer <public_ip_address>/login.php.

<img width="960" alt="output 1" src="https://user-images.githubusercontent.com/111741533/211567635-a4e000cd-ae32-48a0-8b39-f85c6ab805d7.png">

After logging in 

<img width="683" alt="output 2" src="https://user-images.githubusercontent.com/111741533/211567762-7a1e70eb-33f3-440f-a5c7-8a438d29bd62.png">















