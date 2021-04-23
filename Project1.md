## WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS
### Install Apache using Ubuntu’s package manager ‘apt’:
# sudo apt update
# run apache2 package installation
# $ sudo apt install apache2
# check if apache is running
# $ sudo systemctl status apache2
**open pport 80 - to allow our web broowser access web pages on the internet**
![image](https://user-images.githubusercontent.com/70109786/115339120-c5606900-a169-11eb-8e03-10e3d922115b.png)
http://<Public-IP-Address>:80
![image](https://user-images.githubusercontent.com/70109786/115339448-6f3ff580-a16a-11eb-83c5-d59620da8c47.png)

### Installing MySQL
# sudo apt install mysql-server
#install security settings
# sudo mysql_secure_installation
# login to mysql console
# sudo mysql
### Installing PHP
# install these 3 packages
# sudo apt install php libapache2-mod-php php-mysql
# confirm your version
# php -v
# Creating a Virtual Host for your Website using Apache
# Create the directory for projectlamp using ‘mkdir’ command 
# Next, assign ownership of the directory with the $USER environment variable, which will reference your current system user:
# reate and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor. Here, we’ll be using vi or vim
# use a2ensite command to enable the new virtual host:
# if you’re not using a custom domain name, disable Apache’s default website 
(ServerAdmin webmaster@localhost DcumentRoot /var/www/html)  
#ServerAdmin webmaster@localhost
# sudo a2dissite 000-default
# To make sure your configuration file doesn’t contain syntax errors, run:
# sudo apache2ctl configtest
# Reload apache2
# fill up empty file /var/www/projectlamp
sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

![image](https://user-images.githubusercontent.com/70109786/115418247-f61ebd80-a1be-11eb-8927-35966807ba50.png)

sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html

