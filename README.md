# Deploying Python App to Linux Server

This is a project for Udacity's Full-Stack Web Development Nanodegree. 

## Connecting

**Public IP Address:** 34.232.49.36
**SSH Port:** 2200
**URL:** [http://34.232.49.36](http://34.232.49.36)

## Installed Packages

* flask
* postgresql
* sqlalchemy
* apache2
* libapache2-mod-wsgi
* git

## Configuration Steps

1. Update server packages and secure firewall.

```
# Update package list
sudo apt-get update

# Upgrade out-of-date packages
sudo apt-get upgrade

# Change SSH port to 2200
sudo nano /etc/ssh/sshd_config

# Configure firewall to deny incoming requests by default and allow outgoing requests, with the exception of specified ports
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 2200
sudo ufw allow 80
sudo ufw allow 123
```

2. Create account for grader and grant access

```
# Create new account
sudo adduser grader

# Grant sudo permissions
sudo nano /etc/sudoers.d/grader

# On local machine, generate SSH key pair
ssh-keygen

# Place public key on server
mkdir .ssh
touch .ssh/authorized_keys
sudo nano .ssh/authorized_keys 
chmod 700 .ssh
chmod 644 .ssh/authorized_keys
```

3. Prepare to deploy and install packages

```
# Change time zone to UTC
sudo dpkg-reconfigure tzdata

# Install apache and set up mod_wsgi
sudo apt-get install apache2
sudo apt-get install python-setuptools libapache2-mod-wsgi

# Install git
sudo apt-get install git

# Install PostgreSQL
sudo apt-get install postgresql
```

4. Configure PostgreSQL

```
# Login as master user
sudo su - postgre

# Connect
psql 

# Create catalog user and catalog database
CREATE USER catalog WITH PASSWORD 'catalog';
CREATE DATABASE catalog WITH OWNER catalog;
```

5. Clone app

```
# Create folder
sudo mkdir -p /var/www/catalog

# Pull code form Git
sudo git clone git://github.com/xaliceli/nd004-neighborhood-guide.git code
```

6. Configure VirtualHost to serve correct file path

```
# Edit configuration file
sudo nano /etc/apache2/sites-available/catalog.conf
```

7. Configure .wsgi file

```
# Edit .wsgi file
cd /var/www/catalog/code
sudo nano catalog.wsgi 
```

8. Restart Apache

```
sudo service apache2 restart 
```

9. Adapt original code to new requirements, including:

* Change Sqlite references to PostgreSQL engine configurations
* Replace relative paths with absolute paths

10. Run application

```
cd /var/www/catalog/code
python db_setup.py
python db_populate.py
python neighborhood_app.py
```

## Resources

* https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* https://www.codementor.io/devops/tutorial/getting-started-postgresql-server-mac-osx
* http://docs.sqlalchemy.org/en/latest/core/engines.html
* https://www.phusionpassenger.com/library/walkthroughs/deploy/python/ownserver/nginx/oss/trusty/deploy_app.html
