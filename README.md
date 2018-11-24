# Linux_Server_Configuration
## Table of Contents

* [Instructions](#instructions)
* [Resources](#resources)
* [Contributing](#contributing)

## Instructions

1. About the project

The project uses Amazon Lighsail to deploy flask based web application into AWS.


2. How to access the server and the hosted application

- to access the server as grader user run ssh in port 2200: `ssh grader@18.196.23.162 -p 2200 -i ~/.ssh/itemCatKey` 
- to access the hosted web application http://18.196.23.162.xip.io/ 


3. Configurations

- From inside the terminal provided after creating instance, run below:
 - `sudo apt_get update` to update package source list
 - `sudo apt_get upgrade` to actually update installed software
 - `sudo apt_get install finger` install new package finger to display readable format info about users
 - `sudo adduser grader` to create new user grader 
 - `sudo touch /etc/sudoers.d/grader` to create new grader file inside sudoers.d dir
 - `sudo nano /etc/sudoers.d/grader` edit file so grader will have access to run sudo

- From local terminal, run below:
 - `ssh-keygen` to generate public and private key pairs 

- From inside the terminal provided after creating instance, run below:
 - `sudo su - grader` to switch to grader user
 - `mkdir .ssh` to create a new directory
 - `touch .ssh/authorized_keys` to create a new file 
 - `nano .ssh/authorized_keys` to open editor and paste the contect of the locally created public key inside it
 - `chmod 700 .ssh` to set permission on .ssh folder
 - `chmod 644 .ssh/authorized_keys` to set permission on the file
 - `sudo nano /etc/ssh/sshd_config` to open editor and disable passwordAuthentiacation
 - `sudo service ssh restart` to restart so new changes will be reflected
 - `sudo ufw default deny incoming` to configure firewall, first block every incoming request
 - `sudo ufw default allow outgoing` to allow any request server tries to send outside
 - `sudo ufw allow ssh` to allow ssh
 - `sudo ufw allow 2200/tcp` ssh on port 2200
 - `sudo ufw allow www` for http
 - `sudo ufw allow ntp` for ntp
 - `sudo ufw enable` enable firewall
 - `sudo nano /etc/ssh/sshd_config` to open editor and change port number from 22 to 2200
 - `sudo service ssh force-reload` to restart

- Now the provided instance terminal will not be available as it is using ssh port 22 by default, so access the server locally by downloading the SSH key pairs provided inside AWS account and run `ssh -i LightsailDefaultPrivateKey-eu-central-1.pem ubuntu@18.196.23.162 -p 2200`:
 - set UTC timing by:
  - `sudo apt install chrony`
  - `sudo nano /etc/chrony/chrony.conf` to open editor and add server 169.254.169.123 prefer iburst
  - `sudo /etc/init.d/chrony restart` to restart
  - `chronyc sources -v`
  - `chronyc tracking`
 - install apache2, wsgi, postgresql, git, python and other dependencies:
  - `sudo apt-get install apache2`
  - `sudo apt-get install libapache2-mod-wsgi-py3`
  - `sudo apt-get install postgresql postgresql-contrib`
  - `sudo nano /etc/postgresql/9.5/main/pg_hba.conf` to open editor and disable remote connection
  - `sudo apt-get install git-core`
  - `sudo apt-get install python3.6`
  - `sudo apt install python3-pip`
  - `sudo pip3 install SQLAlchemy`
  - `sudo pip3 install flask`
  - `sudo pip3 install --upgrade oauth2client`
 - clone git repo for item catalog:
  - `sudo mkdir /var/www/itemCatApp`
  - `cd /var/www/itemCatApp`
  - `sudo git clone https://github.com/Lujain-sul/Item_Catalog.git`
  - make some changes in python files to match the new IP address
  - make some changes in python files to use postgresql instead of sqlite
  - make some changes in Google Developer Console so that Credentials match the new IP address, change client_secrets.json 
 - configure the database: 
  - `sudo su - postgres` to switch to postgres user
  - `psql` to open psql command 
  - `CREATE DATABASE catalog;` to create catalog db
  - `CREATE USER catalog;` to create catalog user
  - `ALTER ROLE catalog WITH PASSWORD ‘password’;` to edit role
  - `GRANT ALL PRIVILEGES ON DATABASE catalog TO catalog;` to give catalog user all privileges on catalog db
  - `\q` to quit psql command
  - `exit` to exit postgres user 
  - `sudo pip3 install psycopg2-binary;` to install psycopg2 module
  - `sudo python3 database_setup.py` to setup db
  - `sudo python3 load_data.py` for db seeder
 - change the defualt configration to serve the new application:
  - `sudo nano catalogApp.wsgi` to open editor and configure application
  - `cd /etc/apache2/sites-available`
  - `sudo nano catalogApp.conf` to open editor and configure the previously created gateway 
 - enable new configuration and restart server:
  - `cd /var/www/itemCatApp`
  - `sudo a2dissite 000-default.conf`
  - `sudo a2ensite catalogApp.conf`
  - `sudo apache2ctl restart`
  - `sudo service apache2 reload`


## Resources

- https://hk.saowen.com/a/0a0048ca7141440d0553425e8df46b16cdf4c13f50df4c5888256393d34bb1b9
- https://stackoverflow.com/questions/42566296/google-api-authentication-not-valid-origin-for-the-client


## Contributing

This project is built in the fulfillment of Udacity Full Stack Nano Degree requirement, pull requests will not be merged to this project.

