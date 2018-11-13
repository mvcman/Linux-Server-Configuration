# Linux Server Configuration:
Installation of a Linux distribution on a virtual machine and prepare it to host your web application(Item Catalog). It includes installing updates, securing it from a number of attack vectors and installing/configuring web and database servers.

**The EC2 URL is :**  http://ec2-35-165-147-241.us-west-2.compute.amazonaws.com/

**Local IP address:** http://35.165.147.241/

**SSH port- 2200**

**Login with:** == ssh -i ~/.ssh/udacity_key.rsa -p 2200 grader@35.165.147.241 ==

# Generate public Key for udacity_key.rsa :

Go to .ssh folder of your local machine in which you paste your udacity_key.rsa private key.
type this command :- == ssh-keygen == .Then two keys will be generated RSA and RSA.pub, print the public key .
copy and paste this public key in ==/home/grader/.ssh/authorized_keys== so that reviewer can login into grader account .
