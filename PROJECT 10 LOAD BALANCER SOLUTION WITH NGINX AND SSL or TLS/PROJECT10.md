# PROJECT 10 LOAD BALANCER SOLUTION WITH NGINX AND SSL or TLS

This project consists of two parts:


### **- Register a new domain name and configure secured connection using SSL/TLS certificates**


### **- Configure Nginx as a Load Balancer**


The target architecture will look like this:


![image](https://user-images.githubusercontent.com/111741533/219146747-419ee59a-4d26-4de6-b236-5ede6daaba85.png)


## Preliminary 

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx Load Balancer _Nginx LB_ (open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
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

Restart Nginx and make sure the service is up and running

```
sudo systemctl restart nginx
sudo systemctl status nginx
```



- For this project the content of the load balancer config are thus;


<img width="563" alt="image" src="https://user-images.githubusercontent.com/111741533/219171005-5e7eb0b6-dd87-4ab1-865f-bf3ad99818b0.png">



- Remove the default site enable file and ensure only the loadbalancer file is in the folder

<img width="532" alt="image" src="https://user-images.githubusercontent.com/111741533/219167231-e3e47243-3c84-41ea-b7be-359db99b23b7.png">

- go to ```/etc/nginx/site-enabled``` remove the default file in then link it to the load balancer config file then reload the nginx


<img width="541" alt="site enabled config" src="https://user-images.githubusercontent.com/111741533/219169897-32fba8f4-fc19-4752-9655-21a8830b207f.png">


verify your nginx set up with ```sudo nginx -t```

<img width="569" alt="image" src="https://user-images.githubusercontent.com/111741533/219170448-b77ca38a-6fb4-4e94-84cc-9ee24793471a.png">

- Make sure snapd service is active and running

<img width="543" alt="image" src="https://user-images.githubusercontent.com/111741533/219172358-a6c14827-79df-4cc2-adcf-c5afecadd6d6.png">


- Check that the Web Servers can be reached from your browser using new domain name using HTTP protocol – http://toolingnwa.online
- 
![not secure output](https://user-images.githubusercontent.com/111741533/219171527-1eac8115-96ba-4b84-9cbb-c86411935274.png)



- Request your certificate 

```sudo snap install --classic certbot```
(just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file

<img width="844" alt="cert box" src="https://user-images.githubusercontent.com/111741533/219172701-8f248065-e68d-4c29-a933-147ad935a47c.png">


- verify the output on the webserver


![connection secured](https://user-images.githubusercontent.com/111741533/219173005-2cd66479-95d2-4c57-9325-2e19e7fb8b42.png)



