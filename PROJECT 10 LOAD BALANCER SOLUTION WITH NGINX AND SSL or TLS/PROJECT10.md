# PROJECT 10 LOAD BALANCER SOLUTION WITH NGINX AND SSL or TLS

This project consists of two parts:

**- Configure Nginx as a Load Balancer**

**- Register a new domain name and configure secured connection using SSL/TLS certificates**

The target architecture will look like this:


![image](https://user-images.githubusercontent.com/111741533/219146747-419ee59a-4d26-4de6-b236-5ede6daaba85.png)


## CONFIGURE NGINX AS A LOAD BALANCER

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (o open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

```sudo apt update && sudo apt install nginx```   
(this code will Update the instance and Install Nginx)
