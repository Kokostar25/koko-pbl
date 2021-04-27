# WEB STACK IMPLEMENTATION (LEMP STACK)
## Step 1 – Install Nginx Web Server
1. sudo apt update 
2. sudo apt install nginx
3. sudo systemctl status nginx
4. open port 80 - HTTP - curl http://localhost:80
5. [nginx webpage](![image](https://user-images.githubusercontent.com/70109786/116182459-e84aca00-a6e1-11eb-81bb-45b178daf392.png)

## Step 2 - Install MySQL
1.  sudo apt install mysql-server -y
2.  remove insecure default settings - sudo mysql_secure_installation
3.  Answer the questions that follow and set your password
4.  log into mysql - sudo mysql
5.  exit mysql - exit
## Step 3 – Installing PHP
1. install two packages - sudo apt install php-fpm php-mysql
2. sudo apt install php-fpm php-mysql -y
## Step 4 — Configuring Nginx to Use PHP Processor
### Create your own custom directory where your site will be served from.
1. sudo mkdir /var/www/projectLEMP
### change ownership to your own ubuntu system
 a. $ ls -l /var/www/
 b. drwxr-xr-x 2 root root 4096 Apr 27 03:25 html
 c. drwxr-xr-x 2 root root 4096 Apr 27 03:56 projectLEMP
 2. $ sudo chown -R $USER:$USER /var/www/projectLEMP
 a. drwxr-xr-x 2 root   root   4096 Apr 27 03:25 html
 b. drwxr-xr-x 2 ubuntu ubuntu 4096 Apr 27 03:56 projectLEMP
### open a new configuration file in Nginx’s sites-available directory using nano
1. sudo nano /etc/nginx/sites-available/projectLEMP
### Paste in the following bare-bones configuration:
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
### Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

1. sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
#### (note ln -s means ln- link or connect the info in one file to another. -s means a symbolic link rather than a hard link, something dynamic)
2. sudo nginx -t (to test it)
### disable default Nginx host that is currently configured to listen on port 80
3. sudo unlink /etc/nginx/sites-enabled/default
4. sudo systemctl reload nginx
### fill up your webroot directory with some files
5. sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
### test
6. ![image](https://user-images.githubusercontent.com/70109786/116184401-4af19500-a6e5-11eb-8776-ada26c3301ac.png)
7. sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

## Step 5 – Testing PHP with Nginx
### Here you want to make sure nginx can correctly send .php files to the php processor
1.  nano /var/www/projectLEMP/info.php
2.  type <?php
phpinfo();
3. ^x, y and save
4. http://34.207.88.69/info.php
![image](https://user-images.githubusercontent.com/70109786/116185245-d7508780-a6e6-11eb-9c5a-0249f2512675.png)
5. sudo rm /var/www/projectLEMP/info.php 
## Step 6 — Retrieving data from MySQL database with PHP
### We’ll need to create a new user with the mysql_native_password authentication method in order to be able to connect to the MySQL database from PHP.
### create a database named example_database and a user named example_user,
1. sudo mysql
2. CREATE DATABASE `example_database`;
3. CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
4. GRANT ALL ON example_database.* TO 'example_user'@'%';
### test it 
5. mysql -u example_user -p
6. SHOW DATABASES;
7. mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.01 sec)
8. CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id)); CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
9. INSERT INTO example_database.todo_list (content) VALUES ("My Second important item");
Query OK, 1 row affected (0.00 sec)

10. INSERT INTO example_database.todo_list (content) VALUES ("My third import
ant item");
Query OK, 1 row affected (0.01 sec)

11. INSERT INTO example_database.todo_list (content) VALUES ("My final most important item");
Query OK, 1 row affected (0.00 sec)
### (to view output)
12. SELECT * FROM example_database.todo_list; 
13. +---------+------------------------------+
| item_id | content                      |
+---------+------------------------------+
|       1 | My first important item      |
|       2 | My Second important item     |
|       3 | My third important item      |
|       4 | My final most important item |
+---------+------------------------------+
4 rows in set (0.00 sec)
14. mysql> exit
### create a PHP script that will connect to MySQL and query for your content. Create a new PHP file in your custom web root directory using your preferred editor.
15. nano /var/www/projectLEMP/todo_list.php
### Copy this content into your todo_list.php script:
insert <?php
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


16. check your web browser by visiting - http://<Public_domain_or_IP>/todo_list.php

![image](https://user-images.githubusercontent.com/70109786/116188303-d1f63b80-a6ec-11eb-9390-7043e8e97907.png)

