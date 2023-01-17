# LOAD BALANCER SOLUTION WITH APACHE

In this project i will enhance the Tooling Website solution from project 7 by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL.

Let us take a look at the updated solution architecture with an LB added on top of Web Servers (for simplicity let us assume it is a software L7 Application LB, for example – Apache, NGINX or HAProxy)

![image](https://user-images.githubusercontent.com/111741533/212845990-c10aa340-b141-4dd0-9ee9-27ccb8f048ff.png)

### Task
Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

## CONFIGURE APACHE AS A LOAD BALANCER

- Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this:
 
<img width="534" alt="ec2 load balancer" src="https://user-images.githubusercontent.com/111741533/212846778-79a43fe9-11f8-4c45-a8c2-f1e76ff7233d.png">

- Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

<img width="536" alt="port 80" src="https://user-images.githubusercontent.com/111741533/212847028-0f943399-51e5-486d-a8b4-fa283861ea63.png">

- Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

```
#Install apache2
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev
#Enable following modules:
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
#Restart apache2 service
sudo systemctl restart apache2
```


<img width="586" alt="rewrite mode" src="https://user-images.githubusercontent.com/111741533/212848048-34d090f9-811d-4a9b-861a-85d76dfd2433.png">

- Make sure apache2 is up and running

```sudo systemctl status apache2```

<img width="628" alt="restart apache and status" src="https://user-images.githubusercontent.com/111741533/212849790-3f412b92-0624-402f-bf74-39d78a5ef477.png">

### Configure load balancing


```
sudo vi /etc/apache2/sites-available/000-default.conf
#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>
<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>
        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
```



#Restart apache server

```sudo systemctl restart apache2```


<img width="710" alt="edit load balancer and restart apache" src="https://user-images.githubusercontent.com/111741533/212850422-276ab0b7-4fff-44fe-be26-4664ba9ff9aa.png">

This ```bytraffic``` balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by ```loadfactor``` parameter.


- To Verify that our configuration works – try to access the LB’s public IP address or Public DNS name from your browser:

```http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php```


<img width="848" alt="loadbal output" src="https://user-images.githubusercontent.com/111741533/212851975-e1a588f5-8c08-4da0-bb14-a46eab8bc55e.png">


<img width="944" alt="output two for lb" src="https://user-images.githubusercontent.com/111741533/212854560-59e0475f-f4d0-4b63-8cd0-a3459fd98bed.png">


- To verify if each Web Server has its own log directory.

- Open two ssh/Putty consoles for both Web Servers and run following command:

```sudo tail -f /var/log/httpd/access_log```

- Refresh your browser page several times and make sure that both servers receive HTTP GET requests from your LB – new records must appear in each server’s log file.


<img width="943" alt="web 1 potty console" src="https://user-images.githubusercontent.com/111741533/212853385-edc8a15d-72f8-4b65-82b1-adfd86ddf6b3.png">


<img width="956" alt="web 2 potty console" src="https://user-images.githubusercontent.com/111741533/212853493-d91816ba-2871-4773-ba01-196588f79e85.png">


## Configure Local DNS Names Resolution

Sometimes it is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management.

What we can do, is to configure local domain name resolution. The easiest way is to use ```/etc/hosts``` file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So let us configure IP address to domain name mapping for our LB.

```
#Open this file on your LB server
Sudo vi /etc/hosts
#Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers
<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```

- Now we can update your LB config file with those names instead of IP addresses.

```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```

<img width="589" alt="change name on hosts" src="https://user-images.githubusercontent.com/111741533/212854918-effb2bab-e476-4d88-85ae-44ef45c09253.png">


we then curl our Web Servers from LB locally curl http://Web1 or curl http://Web2


<img width="846" alt="curl web 1" src="https://user-images.githubusercontent.com/111741533/212854821-4f576b41-3138-4771-82f9-12ff0f91447a.png">


<img width="857" alt="curl web 2" src="https://user-images.githubusercontent.com/111741533/212854867-79dba4cd-ed3e-4ca6-b2ba-217e6c982b20.png">


The Targed Architecture  set up nowlooks like this:


![image](https://user-images.githubusercontent.com/111741533/212855544-0214cea0-69e5-47fa-b5e8-eb7811c8d25e.png)





