# Web Solution With WordPress
In this project i will be preparing storage infrastructure on two Linux servers and implement a basic web solution using WordPress. WordPress is a free and open-source content management system written in PHP and paired with MySQL or MariaDB as its backend Relational Database Management System (RDBMS). This project also showcases Three-tier Architecture while also certifying that the disks used to store files on the Linux servers are sufficiently partitioned and handled through programs such as gdisk and LVM respectively.

3-Tier Setup

• A Laptop or PC to serve as a client

• An EC2 Linux Server as a web server (housing WordPress)

• An EC2 Linux server as a database (DB) server


<img width="448" alt="image" src="https://user-images.githubusercontent.com/111741533/209454459-cdba43ec-e5bd-4fdb-93c2-59ad83fc1135.png">


### This project 6 consists of two parts:

*Part 1:* Configure storage subsystem for Web and Database servers based on Linux OS: The focus of this part is to give you practical experience of working with disks, partitions and volumes in Linux.

*part 2:* Install WordPress and connect it to a remote MySQL database server: This part of the project will solidify your skills of deploying Web and DB tiers of Web solution.

## Step 1 — Preparing the Web and database Server

Here i created and  configure two Linux-based virtual servers (EC2 instances in RedHat)

Launched the EC2 instances as  database server and “Web Server”. 
I then created 6 volumes of which three were attached to the web server and the other three to the databases server

<img width="827" alt="image" src="https://user-images.githubusercontent.com/111741533/209454572-c3689573-e6c5-4afe-b3a7-7716f795f571.png">

<img width="838" alt="image" src="https://user-images.githubusercontent.com/111741533/209454578-73a32a53-f1b7-41ae-a074-0a170ce49d1c.png">


### on the the database and webserver

Used gdisk utility to create a single partition on each of the 3 disks:

 ```
 sudo gdisk /dev/xvdf
 sudo gdisk /dev/xvdg
 sudo gdisk /dev/xvdh
 ```

<img width="650" alt="image" src="https://user-images.githubusercontent.com/111741533/209454646-c564e1f9-a713-452b-b488-869fd40f628d.png">


Use lsblk utility to view the newly configured partition on each of the 3 disks:

```lsblk```

<img width="853" alt="image" src="https://user-images.githubusercontent.com/111741533/209454691-617a52df-7190-4da1-805c-4a1a0822edcd.png">


Install lvm2 package using "sudo yum install lvm2".

<img width="854" alt="image" src="https://user-images.githubusercontent.com/111741533/209454718-9b0f703c-4baf-407f-8866-2d3b2aa82373.png">


Used ```pvcreate``` utility to mark each of 3 disks as physical volumes (PVs),used ```vgcreate`` for volume grouping to be used by LVM:


<img width="732" alt="image" src="https://user-images.githubusercontent.com/111741533/209454737-b9f7442c-281e-4254-81a2-a968eee81e0f.png">

Used  lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.
```
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
```

Verify the entire setup

```
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```
<img width="566" alt="image" src="https://user-images.githubusercontent.com/111741533/209454785-8081c256-b074-4698-8fad-e2e41585b367.png">

Use mkfs.ext4 to format the logical volumes with ext4 filesystem:

### On the webserver

- sudo mkfs -t ext4 /dev/webdata-vg/apps-lv

- sudo mkfs -t ext4 /dev/webdata-vg/logs-lv

<img width="854" alt="image" src="https://user-images.githubusercontent.com/111741533/209454802-92543b90-0fb4-4348-b8a6-ddaec7143ffb.png">

- Created the /var/www/html directory to store website files:

```sudo mkdir -p /var/www/html```

Created /var/www/html directory to store website files:

```sudo mkdir -p /var/www/html```

Created /home/recovery/logs to store backup of log data:

```sudo mkdir -p /home/recovery/logs```

Mounted /var/www/html on apps-lv logical volume:

```sudo mount /dev/webdata-vg/apps-lv /var/www/html/```

 
Used rsync utility to backup all the files in the log directory /var/log into /home/recovery/logs:

```sudo rsync -av /var/log/. /home/recovery/logs/```

Mounted /var/log on logs-lv logical volume:

```sudo mount /dev/webdata-vg/logs-lv /var/log```

Restored log files back into /var/log directory:

```sudo rsync -av /home/recovery/logs/log/. /var/log```

Updated /etc/fstab file so that the mount configuration will persist after restart of the server, you can get the UUID of the device by running sudo blkid. Update the file with:


Test the configuration and reload the daemon:

```
sudo mount -a
 sudo systemctl daemon-reload
 ```
 
###  on the Database Server

Mount /db on db-lv logical volume:

 ```sudo mount /dev/database-vg/db-lv /db```
 
<img width="709" alt="image" src="https://user-images.githubusercontent.com/111741533/209454876-7d5a7887-fe5c-441c-9df3-b7d115385feb.png">

as done on the webserver, the  /etc/fstab file was updated so that the mount configuration will persist after restart of the server, you can get the UUID of the device by running sudo blkid. 

Tested the configuration and reloaded the daemon:

``` 
sudo mount -a
sudo systemctl daemon-reload
```

## Step 2 — Install Wordpress on your Web Server EC2

this step focused on the intallation process of wordpress on webserver

-Installed wget, Apache and it’s dependencies


```sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json```

- Started Apache

```
sudo systemctl enable httpd
sudo systemctl start httpd
```

- installed PHP and it’s depemdencies

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

<img width="958" alt="output after enabling httpd" src="https://user-images.githubusercontent.com/111741533/209455175-402a62ea-4278-4008-8765-672301494deb.png">


- Downloaded wordpress and copy wordpress to var/www/html

<img width="831" alt="image" src="https://user-images.githubusercontent.com/111741533/209454954-f5ae49ca-1eb9-40ab-9f97-cf0281df1a86.png">

```
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```

- Configured SELinux Policies


 ```
 Sudo chown -R apache:apache /var/www/html/wordpress
 sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 sudo setsebool -P httpd_can_network_connect=1
 ```


<img width="676" alt="image" src="https://user-images.githubusercontent.com/111741533/209455029-244afce3-1358-4c2e-b9a4-f1b320c922d7.png">

  
  
## Step 3 — Install MySQL on  DB Server EC2

Updated and installed mysql server on the DB Server EC2:


<img width="954" alt="image" src="https://user-images.githubusercontent.com/111741533/209455053-a3a8fefd-def5-4b37-a843-4cf3a6119319.png">



## Step 4 — Configured DB to work with WordPress (on db server)

- the defualt setting of mysequel was first adjusted

<img width="764" alt="image" src="https://user-images.githubusercontent.com/111741533/209455070-6176d817-93d9-442a-954f-36d5f839b1d3.png">

- created a database and a user on the mysql

<img width="585" alt="image" src="https://user-images.githubusercontent.com/111741533/209455079-23f30157-2a8e-4d9b-a03a-1af671b8e160.png">


## Step 5 — Configured WordPress to connect to remote database

- Open MySQL port 3306 on DB Server EC2. The acess was opened to all  regardless of IP address

<img width="794" alt="image" src="https://user-images.githubusercontent.com/111741533/209455095-5929b1f2-82d0-4209-80dd-fbde989c8dba.png">

- Install MySQL client and test that you can connect from your Web Server to your DB server:

```
sudo yum install mysql -y
sudo mysql -h 172.31.28.152 -u wordpress -p
```
 
- Verify if I could successfully execute SHOW DATABASES command and see a list of existing databases

<img width="908" alt="image" src="https://user-images.githubusercontent.com/111741533/209455110-a0ecc4ac-88ed-4ff6-9862-3d21784a6cd7.png">

- Updated /etc/my.cnf, add the following to the file:

```
[mysqld]
bind-address=0.0.0.0
```

- Restarted the mysql:

```sudo systemctl restart mysqld```

- Changed permissions and configuration on the webserver so Apache could use WordPress, update the Database name, username and password:

```sudo vi /var/www/html/wp-config.php```


<img width="783" alt="image" src="https://user-images.githubusercontent.com/111741533/209455138-b74dfd42-f2d5-4fb7-beb4-3483d3b9969f.png">


## Step 6: accessing the WordPress from web using Public IP Address of the Webserver:
 
<img width="823" alt="ddddd" src="https://user-images.githubusercontent.com/111741533/209455322-628fad67-f03e-429d-9434-456a0d0bd843.png">


<img width="933" alt="1st wordpress screen" src="https://user-images.githubusercontent.com/111741533/209455178-0037fd2f-8212-4053-a1de-ef5ae59385d9.png">

<img width="943" alt="second screen wordpress" src="https://user-images.githubusercontent.com/111741533/209455184-366bbd93-24b5-4338-a96f-400a2f56607d.png">


<img width="943" alt="image" src="https://user-images.githubusercontent.com/111741533/209455286-6eebe820-3a63-438f-91a7-19dc4338c665.png">






https://user-images.githubusercontent.com/111741533/209455254-955da79a-5aa9-4f08-9b92-2eb0be01a592.mp4


### thank you





