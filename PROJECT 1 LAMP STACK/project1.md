# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
A technology stack is a set of frameworks and tools used to develop a software product. This set of frameworks and tools are very specifically chosen to work together in creating a well-functioning software. They are acronymns for individual technologies used together for a specific technology product. some examples are…
- **LAMP** (Linux, Apache, MySQL, PHP or Python, or Perl)
- **LEMP** (Linux, Nginx, MySQL, PHP or Python, or Perl)
- **MERN** (MongoDB, ExpressJS, ReactJS, NodeJS)
- **MEAN** (MongoDB, ExpressJS, AngularJS, NodeJS

For this project, the focus is on the implementation of a LAMP STACK. this project will be carried ou in four steps. they are; 
 - Step 1 — installing apache and updating the firewall
 - Step 2 — installing mysql
 - Step 3 — installing php
 - Step 4 — creating a virtual host for your website using apache
 - Step 5 — enable php on the website

#### STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL
Apache HTTP Server is the most widely used web server software. Developed and maintained by Apache Software Foundation, Apache is an open source software available for free. It runs on 67% of all webservers in the world. It is fast, reliable, and secure. The Apache web server is among the most popular web servers in the world. It’s well documented, has an active community of users, and has been in wide use for much of the history of the web, which makes it a great default choice for hosting a website.
Install Apache using Ubuntu’s package manager ‘apt’:
To install,  run;

```sudo apt install apache2 ```

To verify that apache2 is running as a Service in our OS, use following command

```sudo systemctl status apache2```

The ouput on the terminal was thus;

<img width="960" alt="Screenshot 2022-10-07 002634" src="https://user-images.githubusercontent.com/111741533/194436235-35994a2c-4fa9-45cc-be5a-8fb9e047c06f.png">

with the active status of the apache server, to check how we can access the server locally in our Ubuntu shell, run:

```curl http://localhost:80```

<img width="950" alt="2" src="https://user-images.githubusercontent.com/111741533/194437015-74d13a59-af53-46a4-8311-92af0a30cac8.png">

with this output, it confirms that the Apache HTTP Server is completely installed

#### Step 2 — INSTALLING MYSQL
MySQL is a popular relational database management system used within PHP environments, so it will use it in this project.

the ‘apt’ is used to acquire and install this software:

```sudo apt install mysql-server```

after reuning the cod the installation confirmation found was thus;

<img width="689" alt="sql 1" src="https://user-images.githubusercontent.com/111741533/194438810-dc33c625-8e91-4b58-9514-31f507f535de.png">


<img width="959" alt="sql 2" src="https://user-images.githubusercontent.com/111741533/194438862-21cdb634-f7bf-4998-8192-865c9ca29f4e.png">

after installation, to remove some insecure default settings, lock down access to the database system and change the default password, the following codes were entered 

``` sudo mysql```

```ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';```


<img width="797" alt="password" src="https://user-images.githubusercontent.com/111741533/194439557-e2987e5b-0351-4fa2-ad0d-d979e53ea2d5.png">


### STEP 3 — INSTALLING PHP

 PHP is the component of our setup that will process code to display dynamic content to the end user. In addition to the ***php package***, i’ll need ***php-mysql***, a PHP module that allows PHP to communicate with MySQL-based databases. i’ll also need ***libapache2-mod-php*** to enable Apache to handle PHP files. Core PHP packages will automatically be installed as dependencies.
 To install these 3 packages at once, run:
 ```sudo apt install php libapache2-mod-php php-mysql```
 
after running the code, the following output was obtained;

<img width="746" alt="php" src="https://user-images.githubusercontent.com/111741533/194440420-f18754eb-baa7-4be1-aed9-44b29ebb4ba4.png">


### STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE

In this step, i set up a domain called ***projectlamp***

to do this, i Created directory for projectlamp using ‘mkdir’ command as follows:

```sudo mkdir /var/www/projectlamp```

Next, i assigned ownership of the directory with my current system user:

```sudo chown -R $USER:$USER /var/www/projectlamp```
 
Then, i created and opened a new configuration file in Apache’s sites-available directory using my preferred command-line editor. Here, i’ll be using vi:

```sudo vi /etc/apache2/sites-available/projectlamp.conf```

this will create a new blank file where the following bare-bones configuration were inserted;

```
<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

To save and close the file, simply follow the steps below:

Hit the esc button on the keyboard
Type :
Type wq. w for write and q for quit
Hit ENTER to save the file



<img width="866" alt="lamp1" src="https://user-images.githubusercontent.com/111741533/194441346-4ada0c2e-d7da-4c35-8bb6-6c65be6102cf.png">


i then used ```a2ensite``` command to enable the new virtual host:

```sudo a2ensite projectlamp```

to make the new virtual host visible, i disabled the default website that comes installed with Apache. To disable Apache’s default website i used ```a2dissite``` command , type:

```sudo a2dissite 000-default```

<img width="482" alt="server 2" src="https://user-images.githubusercontent.com/111741533/194442230-7d161963-4c25-4853-9717-4d449a158e83.png">


Finally, reload Apache so these changes take effect:

```sudo systemctl reload apache2```

the new website is now active, but the web root /var/www/projectlamp is still empty. 

i Created an index.html file in that location so that i can test that the virtual host works as expected:

```
 sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
 ```
 
 <img width="960" alt="server 3" src="https://user-images.githubusercontent.com/111741533/194442605-40e5a382-895a-4ec9-8384-a642d91f906e.png">


to confirm the website is up, opened theIP addres on my browser:

<img width="923" alt="browser" src="https://user-images.githubusercontent.com/111741533/194442969-485fab46-6b74-456f-b613-6c0304df5fa6.png">


### STEP 5 — ENABLE PHP ON THE WEBSITE
With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors
In case you want to change this behavior, you’ll need to edit the ***/etc/apache2/mods-enabled/dir.conf*** file and change the order in which the index.php file is listed within the ***DirectoryIndex*** directive:

``` sudo vim /etc/apache2/mods-enabled/dir.conf ```

```
<IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>
```

After saving and closing the file, i reloaded Apache so the changes take effect:

```sudo systemctl reload apache2```

<img width="698" alt="php 4" src="https://user-images.githubusercontent.com/111741533/194443482-b927819e-ed2d-45d7-a8b8-df3c62aae405.png">


i then created a PHP test script to confirm that Apache is able to handle and process requests for PHP files:

```vim /var/www/projectlamp/index.php````
this will open a blank file. Add the following text, which is valid PHP code, inside the file:

```
<?php
phpinfo();
```

<img width="943" alt="php content" src="https://user-images.githubusercontent.com/111741533/194444693-9af05a35-eb02-4ef0-b549-c97456206a82.png">











