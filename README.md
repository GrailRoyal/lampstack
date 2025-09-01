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
![EC2 Instance](https://github.com/GrailRoyal/lampstack/blob/images/1000756407.png)


## Connecting to the EC2 Instance

Once your instance is running, connect via SSH with the following command:

```bash
ssh -i key.pem ubuntu@<mypublicip>
```
 This command connects your local machine to get access to your cloud machine which is the instance that has been set up . This allows for the Instance to be managed or worked on with your local machine via the terminal 
## installing the LAMP stack
**Update the package repository**
```bash
sudo apt update
```
This command tells your machine to update itself by update its software repositiry for your machine to keep it upto date so it can be able to get the latest software or program when you try to download or upgrade a software por program
**Install Apache**
```bash
sudo apt install apache2
```
The apache prgram is downloaded to your machine with this command , it tells your machine to download apache

**Check Apache2 status**
```bash
sudo systemctl status apache2
```
 This command is used to check the status of the apache that was just downloaded to as to know if its downloaded and running properly 
## Test Apache2 on server
```bash
http://mypublicip
```
![Apache2 Testing](https://github.com/GrailRoyal/lampstack/blob/images/1000756329.png)

## installing mySQL and security
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```
This commands installs the mySQL server and configure the security through the password strenght setting and access control
and configure a more secure access to the mySQL serve orther thatn the default which makes the server more secure

## installing Php
```bash
sudo apt install php libapache2-mod-php php-mysql
```
This will install the php library that llpws php to work with apache as well as the one that allows php to work with mySQL this is very important becaus ewothput this php will stand insolation on the machine without working with other server it needs to work with for this project.

## Setting up a Virtual Host
**create directory for project**
This set up is what what cfrrate the directory from which our server will be serving out content upon request. This is very imporant to set up , so as not to let the default diectory server content which may not be the directory in which our content is situated
```bash
sudo mkdir /var/www/projectlamp
```
Create a new directory in the directory that hold the difault 

```bash
sudo chown -R $USER:$USER /var/www/projectlamp
```
change the ownership  of the project directory as well as the files in the directory this is made possible with the recursive included in the command by -R
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
![PHP Tested](https://github.com/GrailRoyal/lampstack/blob/images/1000756325.png)



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
![PHP Info Page](https://github.com/GrailRoyal/lampstack/blob/images/1000756330.png)
   rewriet this for me dont change anything other than correct spellings and puntuoations and
