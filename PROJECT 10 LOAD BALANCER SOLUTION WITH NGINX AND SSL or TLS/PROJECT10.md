# PROJECT 10 LOAD BALANCER SOLUTION WITH NGINX AND SSL or TLS

This project consists of two parts:


### **- Register a new domain name and configure secured connection using SSL/TLS certificates**


### **- Configure Nginx as a Load Balancer**


The target architecture will look like this:


![image](https://user-images.githubusercontent.com/111741533/219146747-419ee59a-4d26-4de6-b236-5ede6daaba85.png)


## Preliminary 

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (o open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
- 

##  Registering a new Domain

- To kick start this project a new domain (toolingnwa.online) from namecheap is purchased. 


<img width="754" alt="domain pruchased" src="https://user-images.githubusercontent.com/111741533/219153828-8672817e-7373-43ff-bf45-96323c063ceb.png">

- click the manage server option and set t name server option for to custom servers


- From the AWS Route 53 console,  Create a public hosted zone for toolingnwa.online

- for the Route 53 hosted zone to be conected to the domain name,  we need to copy each of the name server from the route 53 into the custom server of the domain (toolingnwa.online) on name cheap.
 
 <img width="557" alt="image" src="https://user-images.githubusercontent.com/111741533/219162896-fc1bf402-3521-4955-8b5b-01458771b078.png">

- we than create record on the route 53 such that the record is pointing to the public ip of the ec2 instance lunched earlier


<img width="571" alt="www records" src="https://user-images.githubusercontent.com/111741533/219161561-be562775-0d99-4052-83f8-e8b963f0c0a9.png">

- After creating all the required records the route 53 set up should look like this 
- 

<img width="575" alt="all records" src="https://user-images.githubusercontent.com/111741533/219164069-13a06038-a573-4024-af95-534d725c5f1a.png">


## CONFIGURE NGINX AS A LOAD BALANCER


- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

```sudo apt update && sudo apt install nginx```   
(this code will Update the instance and Install Nginx)

- Enable and start the nginx

```sudo systemctl enable nginx && sudo systemctl start nginx```   


<img width="799" alt="install and start nginx" src="https://user-images.githubusercontent.com/111741533/219151340-c4045c1d-40b0-4b9a-ac1a-953f710a898b.png">



- Edit the default nginx configuration file (insert the load balancer configuration)

```sudo vi /etc/nginx/nginx.conf```

```
#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;
```

- For this project the content of the load balancer config are thus;

<img width="530" alt="load balancer config file" src="https://user-images.githubusercontent.com/111741533/219165412-e5055899-0f21-4114-a19a-bf0d2f94dcbd.png">





