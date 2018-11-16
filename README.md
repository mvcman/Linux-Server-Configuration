# Linux Server Configuration:
Installation of a Linux distribution on a virtual machine and prepare it to host your web application(Item Catalog). It includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

**The EC2 URL is :**  `http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com`

**Local IP address:** `http://13-233-81-214`

**SSH port- 2200**

**Login with:** `ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@13-233-81-214`

# Generate public Key for udacity_key.rsa :

> Go to .ssh folder of your local machine in which you paste your udacity_key.rsa private key.
> Type this command :- `ssh-keygen` .Then two keys will be generated RSA and RSA.pub, print the public key .
> copy and paste this public key in `/home/grader/.ssh/authorized_keys`so that reviewer can login into grader account .

# Configuration Steps:
**Step 1 :** Launch your Virtual Machine with your Udacity account
Development Environment Information Details:-
Public IP Address - 13-233-81-214
Private Key - Can't be shared
**Step-2:** Follow the instructions provided to SSH into your server
mv ~/Downloads/udacity_key.rsa ~/.ssh/
> chmod 600 ~/.ssh/udacity_key.rsa
> ssh -i ~/.ssh/udacity_key.rsa ubuntu@13-233-81-214
**Step-3 :** Create a new user named grader
sudo adduser grader
To check the User(grader) information :
    sudo apt-get install finger
    finger grader
It is give you additional information(login , name , shell, directory, phone number etc) of User-grader

**Step-4 :** Give the grader the permission to sudo by using followin command
`sudo adduser grader sudo`

**Step-5 :** Update all currently installed packages
`sudo apt-get update` - command will update list of packages and their versions on your machine.
`sudo apt-get upgrade` - command will install the packages

**Step-6 :** Change the SSH port from 22 to 2200
`ubuntu@ip-172-26-9-175:~$ sudo su`
`root@ip-172-26-9-175:/# nano /etc/ssh/sshd_config`
change port from `22` to `2200`
change `PermitRootLogin without-password` to `PermitRootLogin no`. It is disable root login.
change PasswordAuthentication from `no` to `yes`.
add AllowUsers grader at end of the file so that we will login through grader.
restart the SSH service : `sudo service ssh restart`
**Step-7 :** SSH- Keys
generate key-pair with `ssh-keygen`on your local machine
Save keygen file into `(/home/user/.ssh/linuxProject)`and fill the password . Two keys will be generated, public key (linuxProject.pub) and identification key(linuxProject).
**Follow the following steps:**
`ubuntu@ip-172-26-9-175:~$ sudo grader`
Enter password for user grader.
make a directory in grader account : `mkdir .ssh`
make a authorized_keys file using touch .ssh/authorized_keys
from your local machine,copy the contents of public key(linuxProject.pub).
paste that contents on authorized_keys of grader account using nano authorized_keys and save it .
give the permissions : `chmod 700 .ssh and chmod 644 .ssh/authorized_keys`.
do `nano /etc/ssh/sshd_config `, change PasswordAuthentication to no .
`sudo service ssh restart`.
`ssh -i ~/.ssh/linuxproject5 grader@13.233.81.214 -p 2200` in new terminal .A pop-up window will open for authentication. just fill the password that you have fill during ssh-keygen creation.

**Step-8:**Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)
check the firewall status using sudo ufw status.
block all incoming connections on all ports using `sudo ufw default deny incoming`.
allow outgoing connections on all ports using `sudo ufw default allow outgoing`.
allow incoming connection for SSH(port 2200) using `sudo ufw allow 2200/tcp`.
allow incoming connection for HTTP(port 80) using `sudo ufw allow 80/tcp`.
allow incoming connection for NTP(port 123) using `sudo ufw allow 123/udp`.
check the added rules using `sudo ufw show added`.
enable the firewall using `sudo ufw enable`.
check whether firewall is enable or not using `sudo ufw status`.

**Step-9:**Install and configure Apache to serve a Python mod_wsgi application:
install apache using `sudo apt-get install apache2` .
type `13.233.81.214` (public IP address) on URL . You will see the apache ubuntu default page .
Install mod_wsgi using `sudo apt-get install libapache2-mod-wsgi python-dev` .


**Step 10:** Install Git and clone your project
Install git using `sudo apt-get install git`
set up git using :
        git config --global user.name "username"
        git config --global user.email "email@domain.com"
check the configurations items using git config --list
make a directory FlaskApp in /var/www/ folder by using `mkdir FlaskApp` command.
Clone Item Catalog project from github by using in /var/www/FlaskApp/ directory by using :`git clone "path to your project"`
For example: `git clone "https://github.com/mvcman/My-Item-Catalog-Employee"` and rename our project folder to `FlaskApp`
then our project directory should look like this `/var/www/FlaskApp/FlaskApp/`
You can delete unwanted files in your folder (for example - readme, vagrant folder etc) and your folder should look like :
grader@ip-172-26-9-175:/var/www/FlaskApp/FlaskApp ls
add_employee.py      employee.travis.yml     `_init_.py`         static
client_secrets.json  fb_client_secrets.json  README.md         templates
database_setup.py    flaskapp.wsgi           requirements
database_setup.pyc   Images                  requirements.txt


**Step 11:** Writing .conf and .wsgi files
This include Following steps :

step-1:
move to the `/var/www/FlaskApp/FlaskApp` directory:
Create `flaskapp.wsgi` file in that folder where our app.py file. 
Write following code to your flaskapp.wsgi file

`#!/usr/bin/python`
`import sys`
`import logging`
`logging.basicConfig(stream=sys.stderr)`
`sys.path.insert(0,"/var/www/FlaskApp/FlaskApp/")`

`from _init_ import app as application`
`application.secret_key = 'your application secrate`


step-2
Now , we will create a virtual environment for our flask application. use pip to install virtualenv and Flask. Install pip : sudo apt-get install python-pip
Install virtualenv: `sudo pip install virtualenv`
Set enviornment name using : `sudo virtualenv venv`
Install Flask in that environment by activating the virtual environment using : `source venv/bin/activate`
Install Flask using : `sudo pip install Flask`
Run the following command to test if the installation is successful and the app is running: `sudo python __init__.py`
It should display `"Running on http://127.0.0.1:5000/"`. If you see this message, you have successfully configured the app.
To deactivate the environment : `deactivate`

step-3
Run - sudo nano /etc/apache2/sites-available/FlaskApp.conf
configure the virtual host adding your Servername:
    <VirtualHost *:80>
        ServerName 13.233.81.214
        ServerAdmin ec2-13-233-81-214.ap-south-1.compute.amazonaws.com
        ServerAlias ec2-13-233-81-214.ap-south-1.compute.amazonaws.com
        WSGIScriptAlias / /var/www/FlaskApp/FlaskApp/flaskapp.wsgi
        <Directory /var/www/FlaskApp/FlaskApp/>
            Order allow,deny
            Allow from all
        </Directory>
        Alias /static /var/www/FlaskApp/FlaskApp/static
        <Directory /var/www/FlaskApp/FlaskApp/static/>
            Order allow,deny
            Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Save and close the file.

Enable virtual host using : `sudo a2ensite FlaskApp`
Note----

You may be confuse in what I have written `ServerAdmin ec2-13-233-81-214.ap-south-1.compute.amazonaws.com`. This is URL for my IP address you can get URL for Your IP address you will get over [hcidata]



Directory structure should be :
|--------FlaskApp
|----------------FlaskApp
|-----------------------static
|-----------------------templates
|-----------------------venv
|-----------------------__init__.py
|-----------------------catalog.wsgi

step-4
Restart Apache : `sudo service apache2 restart`
Resources - Install flask, Virtual Env

**Step-12 :** Install other Packages
go to our folder /var/www/FlaskApp/FlaskApp/ and run following commands
`sudo apt-get install python-pip`
`source venv/bin/activate`
`pip install httplib2`
`pip install requests`
`sudo pip install --upgrade oauth2client`
`sudo pip install sqlalchemy`
`pip install Flask-SQLAlchemy`

If you want to see what packages have been installed with your installer tools : `pip freeze`

**Step 13:** Install Postgresql
Install the Python PostgreSQL adapter psycopg: `sudo apt-get install python-psycopg2`
Install PostgreSQL: `sudo apt-get install postgresql postgresql-contrib`
To check, no remote connections are allowed : `sudo vim /etc/postgresql/9.3/main/pg_hba.conf`
open database_setup.py using : `sudo nano database_setup.py`
update the create_engine line: `engine = create_engine('postgresql://catalog:catalog-pw@localhost/catalog')`
Update the create_engine line in _init_.py and add_employee.py too.
Change to default user postgres: `sudo su - postgre`
Connect to the system: `psql`
Create user catalog: `CREATE USER catalog WITH PASSWORD 'catalog-pw';`
check lists of roles using :`\du`
Allow the user to create database : `ALTER USER catalog CREATEDB;` and check the roles and attributes using `\du`.
Create database using : `CREATE DATABASE catalog WITH OWNER catalog;`
Connect to database using : `\c catalog`
Revoke all the rights : `REVOKE ALL ON SCHEMA public FROM public;`
Grant the access to catalog: `GRANT ALL ON SCHEMA public TO catalog;`
Delete your .db file
Once you execute database_setup.py , again you can login as psql and check all the tables with following commands:
connect to database using : `\c catalog`
To see the tables in schema : `\dt`
to see particular table: `\d [tablename]`
to see the entries/data in table :`select * from [tablename];`
to drop the table:`drop table [tablename];`
exit from Postgresql : `\q then exit from postgresql user.`
restart postgresql: `sudo service postgresql restart`


**Step 14:** Run the application:
Create the datbase schema: python `database_setup.py` python `add_employee.py`

Restart Apache : `sudo service apache2 restart`

in /var/www/FlaskApp/FlaskApp directory : execute - `python __init__.py`

type public IPaddress: `http://13.233.81.214/` or URL:`http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com` and you will see your Restaurant Webpage.


related to client_secrets.json and fb_client_secrets.json files. You need to give absolute path to these files . change the CLIENT_ID = json.loads( open('client_secrets.json', 'r').read())['web']['client_id'] to

open(r'/var/www/FlaskApp/FlaskApp/client_secrets.json', 'r').read())['web']['client_id']```
Similarly for `fb_client_secrets.json` file.

check your errors in /var/log/apache2/error.log files. tail -10 /var/log/apache2/error.log to see last 10 lines of file.

Make sure after you recorrect your error , restart the apache2 server.


**Step 15:** Oauth Login
go to [hcidata] and get the host name of public IP address (13.233.81.214) (IP Address) 13.233.81.214 = (Host Name) http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com

sudo nano /etc/apache2/sites-available/FlaskApp.conf and add the hostname below ServerAdmin: paste ServerAlias http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com

enable the virtual host : `sudo a2ensite catalog`

restart the apacheserver : `sudo service apache2 restart`.

Google Authorization steps:

Go to console.developer
click on Credentails --> edit
add you hostname (http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com ) and public IP address (http://13.233.81.214) to Authorised JavaScript origins.
add hostname (http://ec2-13-233-81-214.ap-south-1.compute.amazonaws.com/oauth2callback) to Authorised redirect URIs.
update the client_secret.json file too(adding hostname and public IP address).
Facebook Authorization steps:

Go to developer.facebook
open your application and click on Facebook Login --> settings.
Add hostname and public IP address to Valid OAuth redirect URIs and save it.
update the fb_client_secret.json file too.






