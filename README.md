
# # Setting Up a LAMP Stack on an EC2 Instance (Ubuntu Server)

Setting up a LAMP stack (Linux, Apache, MySQL, PHP) on an Amazon EC2 instance using Ubuntu Server 22.04 LTS. This setup is ideal for developing and hosting dynamic web applications.

## Components of a LAMP Stack
- **Linux**: The operating system (Ubuntu Server 22.04 LTS).
- **Apache**: The web server that hosts web pages.
- **MySQL**: The database server for managing relational databases.
- **PHP**: The server-side scripting language for web development.

---

## Launching an EC2 Instance

1. **Sign in** to the AWS Management Console.
2. Navigate to the **EC2 Dashboard** and select "Launch Instance."
3. Choose **Ubuntu Server 22.04 LTS** as your operating system.
4. Configure your instance details, including instance type, network, subnet, and security group.
5. Allocate storage based on your requirements.
6. Set up a **Security Group** with inbound traffic rules:
   - Port 22 (SSH)
   - Port 80 (HTTP)
   - Port 443 (HTTPS)
![EC2 Instance](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/ec2.png)


## Connecting to the EC2 Instance

Once your instance is running, connect via SSH with the following command:

```bash
ssh -i key.pem ubuntu@<mypublicip>
```
 
## installing the LAMP stack
**Update the package repository**
```bash
sudo apt update
```
**Install Apache**
```bash
sudo apt install apache2
```
**Check Apache2 status**
```bash
sudo systemctl status apache2
```
## Test Apache2 on server
```bash
http://mypublicip
```
![Apache2 Testing](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/Apache2%20Testing.png)

## installing muSQL and security
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```
this commands installs the mySQL server and configure the security throuugh the passord strnght setting and acess control
![MySQL Installed](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/mySQL%20installed.png)


## installing Php
```bash
sudo apt install php libapache2-mod-php php-mysql
```
![PHP Installation](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/php%20installation.png)


## Setting up a Virtual Host
**create directory for project**
```bash
sudo mkdir /var/www/projectlamp
```
change the ownership  of the project directory as well as the files in the directory this is made possible with the recursive included in the command by -R

```bash
sudo chown -R $USER:$USER /var/www/projectlamp
```
**Configure a virual host**
```bash
sudo nano /etc/apache2/sites-available/projectlamp.conf
```
```bash
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName projectlamp
    ServerAlias www.projectlamp
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

the  code above creates and modify  confguration for virtual host
**Enabling virtual host**
```bash
sudo a2ensite projectlamp
```
 this is to tell the Apache2 to enble the projectlamp configuration
```bash
sudo a2dissite 000-default
```
this tells Apache2 to disable the default configuration , this is important so as to allow Apache2 no use the default onfiguration and use the newly enabled configuration
```bash
sudo systemctl reload
```
to reload Apache2 so the new changes made can be used , this reload is always needed when working with apache2 and chages are made


## Testing Setup

created a file in project root diectory 
```bash
sudo echo 'Hello LAMP from hostname' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html
```
visit project website on browser with webname or ip address
http://mypublicip
![PHP Tested](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/php%20tested.png)



## Enabling PHP
To enable php you would have to update the direcoty index configurastion 
```bash
sudo nano /etc/apache2/mods-enabled/dir.conf
```

```bash
DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
```
its very important the configuration starts with index.php like the congiguration ave

**reload php**
```bash
sudo systemctl reload apache2
```
this will reload the apache2 and php as well, php is a module attached the the apache server so when the apache is reloaded the php reloads as well 

## Testing PHP
create a new php file to displayy the info for php to see if its working well
echo "<?php phpinfo(); ?>" > /var/www/projectlamp/info.php
this command is suppose to display the content in the string but its passed to the php file in the projectlamp directory.
test the file by visting it through the browser by going to the page
http://mypublicip/info.php
![PHP Info Page](https://github.com/GrailRoyal/StegOps/blob/ed6b0c291df66c2c80e7b20a9e6fb8688fa590dd/php%20info%20page.png)
