# Project 5 - Implementing a Client-Server Architecture Using MySQL

## INTRO
Client-Server refers to an architecture in which two or more computers are connected together over a network to send and receive requests between one another.

In their communication, each machine has its own role: the machine sending requests is usually referred as "Client" and the machine responding (serving) is called "Server".

## TASK – Implement a Client Server Architecture using MySQL Database Management System (DBMS)

To demonstrate a basic client-server using MySQL Relational Database Management System (RDBMS), follow the below instructions

- Create and configure two EC2 instances in AWS.

```
Server A name - `mysql server`
Server B name - `mysql client`
```

<img width="812" alt="instance" src="https://user-images.githubusercontent.com/111741533/204486144-c70e4ff9-45d4-479c-83c4-4355e1f16382.png"  width="100" height="100">


- On ```mysql server``` Linux Server install MySQL Server software

```
sudo apt install mysql-server
```

- On ```mysql client``` Linux Server install MySQL Client software.

<img width="739" alt="instal client mysql " src="https://user-images.githubusercontent.com/111741533/204489113-5a5c5a48-8129-4bce-a77c-1f8b2192b85c.png">

- check the ip address of the ```mysql client``` used for allowing access in the ```mysql-server```


<img width="744" alt="show ip address used for allowing access" src="https://user-images.githubusercontent.com/111741533/204490284-2c875325-3d59-4706-86a0-7031c29c8f8b.png">

- Open port 3306 on ```mysql-server``` by adding inbound rule in EC2. Allow access only to mysql-client:

<img width="700" alt="open port on ec2" src="https://user-images.githubusercontent.com/111741533/204489378-56cb7c63-168e-4771-a705-640b16588a79.png" width="700">

- Edit the mysqld.cnf to configure MySQL to allow incoming remote connections:
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
- Find and change the bind-address value from ```127.0.0.1``` to ```0.0.0.0```

<img width="626" alt="editted config in mysql" src="https://user-images.githubusercontent.com/111741533/204491022-e872b421-fff3-4019-949e-3d037c379660.png">


- Enable the mysql with ```systemctl enable```

<img width="727" alt="systemctl enable" src="https://user-images.githubusercontent.com/111741533/204492786-e3639419-54b0-4d45-b725-e41c278e68a0.png">


- Remove insecure default setting with pre-installed security script using ```ALTER USER```:


<img width="682" alt="alter root  user password in mysql" src="https://user-images.githubusercontent.com/111741533/204491651-140a5e62-a90c-4dd1-ac6e-8736c9fe18bd.png">


- log in as root user with new credentials on mysql

<img width="642" alt="log in with new credentials on mysql" src="https://user-images.githubusercontent.com/111741533/204491994-8ba44390-9cf7-4cd6-b1c2-6d766e491062.png">

- create the remote user and the database

<img width="712" alt="creating the remote user and the database" src="https://user-images.githubusercontent.com/111741533/204492223-65636370-e2ab-470b-8981-78b20a4edb47.png">

- Connect to MySQL on mysql-server remotely and show database:

<img width="670" alt="log into the mysql server remotely " src="https://user-images.githubusercontent.com/111741533/204493123-7ed7cfcb-c4bd-4066-9e35-3f781f566823.png">







