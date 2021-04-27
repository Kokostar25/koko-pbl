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

