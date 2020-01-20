# 01202020
## wordpress on aws ec2

### Step 1:
First things first: you need to create your AWS account. To create a AWS account, follow the link given below.
https://portal.aws.amazon.com/billing/signup?redirect_url=https%3A%2F%2Faws.amazon.com%2Fregistration-confirmation#/start
You’ll have to provide a credit card and a phone number where you will be called as part of the online registration process for verification purposes. Amazon offers a Free Usage Tier, which is great to explore the services and even host real apps without being charged. After creating the aws account, login to AWS Management console and select EC2 from the compute services.

### Step 2:
There are multiple options to host a wordpress application using AWS EC2 insstance. The easiest way is to launch a EC2 instance which is readily available with most of our requirement.
To create a new instance,
  - Access the AWS Management Console and click the EC2 tab.
  - Click on "Launch Instance" button.
  - Choose an AMI in the classic instance wizard: I chose the Amazon Linux 2 AMI which is eligible for free tier.
  - Instance details: Select the Instance Type you want to use. I chose t2.micro.
  - You can use an existing key pair or create a new one if you dont have any. Enter a name for your key pair (i.e. key) and download your key pair (i.e. key.pem).
  - Select the quick start security group.
  - Launch your instance.

### Step 3:
Once your instance is running, you can ssh into it. First, you need to identify the address of your instance: Select the instance in the AWS Management Console, and look for the Public DNS in the instance description (bottom part of the screen).

Use that address (and a path to your .pem file) to ssh into your instance:
``` sh
ssh ec2-user@ec2-50-17-14-16.compute-1.amazonaws.com -i ~/key.pem
```
In case of a new key pair, you may get a message about your .pem file permissions being too open. In that case chmod your .pem file as follows:
```sh
chmod 600 ~/key.pem
```
Many of the shell commands below require root access. To avoid having to prefix these commands with sudo, let’s just switch user once and for all:
```sh
sudo su
```
### Step 4:
To install the Apache Web Server, type:
```sh
yum install httpd
```
To start the Apache Web Server:
```sh
service httpd start
```
To test your Web Server, open a browser and access your web site: http://ec2-50-17-14-16.compute-1.amazonaws.com (Use your actual public DNS name). You should see a standard Amazon place holder page.
### Step 5:
To install PHP, type:
```sh
yum install php php-mysql
```
Restart the Apache Web Server:
```sh
service httpd restart
```
Create a page to test your PHP installation:
```sh
cd /var/www/html
vi test.php
```
- Type i to start the insert mode
- Type <?php phpinfo() ?>
- Type :wq to write the file and quit vi

Open a browser and access test.php to test your PHP installation: http://ec2-50-17-14-16.compute-1.amazonaws.com/test.php (Use your actual public DNS name).
### Step 6:
To install MySQL, type:
```sh
yum install mysql-server
```
Start MySQL:
```sh
service mysqld start
```
Create your “blog” database:
```sh
mysqladmin -uroot create blog
```
Secure your database:
```sh
mysql_secure_Installation
```
Answer the wizard questions as follows:

    Enter current password for root: Press return for none
    Change Root Password: Y
    New Password: Enter your new password
    Remove anonymous user: Y
    Disallow root login remotely: Y
    Remove test database and access to it: Y
    Reload privilege tables now: Y
### Step 7:
To install WordPress, type:
```sh
cd /var/www/html
wget http://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gzcd
```
This will uncompress WordPress in its own “wordpress” directory. I like having WordPress in a separate directory, but would rather rename it to “blog”:
```sh
mv wordpress blog
```
Create the WordPress wp-config.php file:
```sh
cd blog
mv wp-config-sample.php wp-config.php
vi wp-config.php
```
- Type i to start insert mode.
- Modify the database connection parameters as follows:
- define(‘DB_NAME’, ‘blog’);
- define(‘DB_USER’, ‘root’);
- define(‘DB_PASSWORD’, ‘YOUR_PASSWORD’);
- define(‘DB_HOST’, ‘localhost’);
- Type :wq to write the file and quit vi

Open a Browser and access your blog:
http://ec2-50-17-14-16.compute-1.amazonaws.com/blog (Use your actual public DNS name).
This should trigger the WordPress configuration process.
### Step 8:
The following can be performed if you have to publish the blog.
- Associate an IP address to your instance
- Map your domain name to that IP address

To associate an IP address to your instance:
- In the AWS Management Console, click Elastic IPs (left navigation bar)
- Click Allocate New Address, and confirm by clicking the “Yes, Allocate” button
- Right-click the newly allocated IP address and select “Associate” in the popup menu.
- Select the instance you just created and click “Yes, Associate”

To map your domain name to your IP address, you will have to use the tools provided by your domain registrar. It varries depending on the service provider.