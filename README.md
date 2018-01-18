# Linux server configuration

## Description
Take a baseline installation of Linux distribtuion on a virtual machine and configure it to deploy web applications including updated dependencies, securing it from a number of attack vectors and installing and configuring web and database servers.


## Important info

The application deployed can be found at: http://ec2-54-201-195-77.us-west-2.compute.amazonaws.com/

IP address: 54.201.195.77

Accessible SSH port: 2200

Password for grader: udacity

## Key steps and software installed
1) Created an encryption key called udacity_key.rsa
2) Changed owner from root to grader and disabled SSH login from root user. 
3) Enforced key-based authentication and changed SSH port from 22 to 2200
4) Confiured firewall to only allow connections from port 2200, port 80, port 123.
5) Installed Apache2, mod_wsgi, Git, Virtual environment in cloned repo (https://github.com/N-Turner/catalog_app).
6) Within virtual environment installed Flask, httplib2, request, oauth2client, sqlalchemy, python-psycoph2.
7) Created catalog.wsgi file:
```python
#!/usr/bin/python

import sys
import logging

activate_this = '/var/www/catalog/catalog/venv/bin/activate_this.py’
execfile(activate_this, dict(file=activate_this))

logging.basicConfig(stream=sys.stderr)
sys.path.insert(0, “/var/www/catalog/”)
sys.path.insert(1, “/var/www/catalog/catalog/”)

from catalog import app as application
application.secret_key = ‘super_secret_key’

```
8) Enabled the virtual host: 
```
<VirtualHost *:80>
ServerName mywebsite.com
ServerAdmin admin@mywebsite.com
WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
WSGIProcessGroup catalog
WSGIScriptAlias / /var/www/catalog/catalog.wsgi
<Directory /var/www/catalog/catalog/>
Options FollowSymLinks
Order allow,deny
Allow from all
Alias /static /var/www/catalog/catalog/static
<Directory /var/www/catalog/catalog/static/>
Options Indexes FollowSymLinks MultiViews
AllowOverride None
Order allow,deny
Allow from all
ErrorLog ${APACHE_LOG_DIR}/error.log
LogLevel warn
CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
9) Installed and configured PostgreSQL with user catalog.

10) Updated OAuth authorzied JavaScript origins on Google and Facebook developer dashboards. 