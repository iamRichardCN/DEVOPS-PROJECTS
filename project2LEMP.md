## WEB STACK IMPLEMENTATION (LEMP STACK)
The implementation of LEMP stack in this project will be carried out in six steps
Step 1 – installing the nginx web server
Step 2 — installing mysql
Step 3 – installing php
Step 4 — configuring nginx to use php processor
Step 5 – testing php with nginx
Step 6 – retrieving data from mysql database with php (continued)


### Step 1 – installing the nginx web server
the apt package manager was used to get Nginx installed using 

```sudo apt update
sudo apt install nginx
```

<img width="564" alt="image" src="https://user-images.githubusercontent.com/111741533/194782984-7de54f08-f7b1-41fb-9f44-16f150c5bdcd.png">


to test how our Nginx server can respond to to local request run;

``` curl http://localhost:80```


<img width="783" alt="image" src="https://user-images.githubusercontent.com/111741533/194783171-6f9ae7a0-f193-4f93-8ada-7ab742c1be60.png">

the output is the same when accessed from web browser

<img width="864" alt="welcome to nginx" src="https://user-images.githubusercontent.com/111741533/194783097-c2892de5-9ec0-4e49-a342-e902c8e8f766.png">

### STEP 2 — INSTALLING MYSQL
the sql was needed as a Database Management System (DBMS) to  store and manage data

the command to run install the sql, use ‘apt’ to acquire and install this software:

```sudo apt install mysql-server```

<img width="665" alt="image" src="https://user-images.githubusercontent.com/111741533/194783418-6d7a9d2e-6bfd-48b4-90f2-32ee39ce07b3.png">


### Step 3 – installing php
the PHP is intalled to process code and generate dynamic content for the web server. it requires the installation of the php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies. to install run 

```sudo apt install php-fpm php-mysql```


### Step 4 — configuring nginx to use php processor
 
When using the Nginx web server, we can create server blocks (similar to virtual hosts in Apache) to encapsulate configuration details and host more than one domain on a single server. In this task, i used use projectLEMP as an example domain name. configure, run;

```Sudo mkdir /var/www/projectLEMP```
Next, assign ownership of the directory:
```sudo chown -R $USER:$USER /var/www/projectLEMP```
Then, open a new configuration file in Nginx’s sites-available directory using  preferred command-line editor. Here, we’ll use nano:
```sudo nano /etc/nginx/sites-available/projectLEMP```
This will create a new blank file. Paste in the following bare-bones configuration:

<img width="868" alt="GNU NGIX " src="https://user-images.githubusercontent.com/111741533/194783702-45642563-68a1-4483-be14-dead394aecd0.png">

 reload Nginx to apply the changes:

```sudo systemctl reload nginx```

Create an index.html file in that location to test that your new server block works as expected. Run 

```
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```



<img width="960" alt="after the inserting hello" src="https://user-images.githubusercontent.com/111741533/194783783-794e87c4-3068-446e-ad67-c05efb73a40d.png">


LEMP stack is now fully configured. however, a PHP script will be added in the next step to  test that Nginx is in fact able to handle .php files within the newly configured website

### STEP 5 – TESTING PHP WITH NGINX
this can be done by creating a test PHP file in the document root. Run; 

```sudo nano /var/www/projectLEMP/info.php```
paste the following lines into the new file

```
<?php
phpinfo();
```
the page can be accessed in your web browser by visiting the domain name or public IP address, followed by /info.php:

<img width="955" alt="php output" src="https://user-images.githubusercontent.com/111741533/194784025-cc5003ac-63d9-4c64-aa5f-c864cfe00558.png">


After checking the relevant information about the PHP server through that page, it’s best to remove the file  created as it contains sensitive information about the PHP environment and in the Ubuntu server. Run this:

```sudo rm /var/www/your_domain/info.php```

<img width="506" alt="image" src="https://user-images.githubusercontent.com/111741533/194784114-84a7bada-84c3-455a-b15f-4020e6b2fe54.png">


### STEP 6 – RETRIEVING DATA FROM MYSQL DATABASE WITH PHP (CONTINUED)
In this step i created a test database (DB) with simple "To do list" and configure access to it, so the Nginx website would be able to query data from the DB and display it.

We will create a database named example_database and a user named example_user

First, connect to the MySQL console using the root account:

```sudo mysql```

mysql> CREATE DATABASE `example_database`;
Now you can create a new user and grant him full privileges on the database you have just created.

The following command creates a new user named example_user, using mysql_native_password as default authentication method. We’re defining this user’s password as password, but you should replace this value with a secure password of your own choosing.

```mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';```

Now we need to give this user permission over the example_database database:

```mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';```


Next, i’ll create a test table named todo_list. From the MySQL console, run the following statement:

```
CREATE TABLE example_database.todo_list (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
   );
   ```
<img width="843" alt="image" src="https://user-images.githubusercontent.com/111741533/194784350-0019d99a-b2c4-4787-951f-1e81f8d45480.png">

after creating the table, the following PHP script connects to the MySQL database and queries for the content of the todo_list table


```
nano /var/www/projectLEMP/todo_list.php
```

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```

 this page can be accessed in  web browser by visiting the domain name or public IP address configured for your website, followed by /todo_list.php
 
 
 <img width="937" alt="image" src="https://user-images.githubusercontent.com/111741533/194784464-6b9ad304-4ed7-4970-86db-778e19d26047.png">


