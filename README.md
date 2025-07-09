# ERPNext  Installation and Production Setup on Ubuntu 22.04

## Prerequisites

- Ubuntu 22.04 (updated)
- User with sudo privileges (non-root)
- Python 3.10+
- Node.js 18
- Minimum 4GB RAM and 40GB Disk space

---

## Step 1: Update & Upgrade System Packages

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```
## Step 2: Create a New User for Bench
Replace [frappe-user] with your desired username (e.g., frappe):

```bash
sudo adduser [frappe-user]
sudo usermod -aG sudo [frappe-user]
su - [frappe-user]
cd /home/[frappe-user]
```
##  Step 3: Install Required Packages
 #### Install Git
```bash
sudo apt-get install git -y
Install Python 3.10 and Development Tools
```
```bash
sudo apt-get install python3-dev python3.10-dev python3-setuptools python3-pip python3-distutils -y
```
#### Install Python Virtual Environment
```bash
sudo apt-get install python3.10-venv -y
```
#### Install Software Properties Common
```bash
sudo apt-get install software-properties-common -y
```
#### Install MariaDB Server and Client

```bash
sudo apt install mariadb-server mariadb-client -y 
```
#### Install Redis Server
```bash
sudo apt-get install redis-server -y
```
#### Install Other Dependencies
```bash
sudo apt-get install xvfb libfontconfig wkhtmltopdf libmysqlclient-dev -y
```
## Step 4: Secure and Configure MariaDB
#### Run the secure installation script:
```bash
sudo mysql_secure_installation
```
Follow the prompts:
```
Enter current root password: (press Enter if none)

Switch to unix_socket authentication? Y

Change root password? Y (set a strong password)

Remove anonymous users? Y

Disallow root login remotely? N (allows remote DB access)

Remove test database? Y

Reload privilege tables? Y
```
## Step 5: Configure MariaDB Charset for UTF8MB4
#### Edit the MariaDB config file:
```bash
sudo nano /etc/mysql/my.cnf
```
Add these lines:
```bash
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci

[mysql]
default-character-set = utf8mb4
```
  Save and exit.

Restart MariaDB:

```bash
sudo service mysql restart
```
## Step 6: Install CURL, Node.js, NPM and Yarn
#### Install CURL
```bash
sudo apt install curl -y
```
#### Install Node.js v18 with NVM
```bash
curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
source ~/.profile
nvm install 18
nvm use 18
```
#### Install NPM
```bash
sudo apt-get install npm -y
```
#### Install Yarn Globally
```bash
sudo npm install -g yarn
```
## Step 7: Install Frappe Bench CLI
```bash
sudo pip3 install frappe-bench
```
## Step 8: Initialize Frappe Bench
```bash
bench init --frappe-branch version-15 frappe-bench
cd frappe-bench
```
## Step 9: Set Permissions for Home Directory
Replace [frappe-user]:

```bash
chmod -R o+rx /home/[frappe-user]
```
## Step 10: Create a New Site
```bash
bench new-site [site-name]
```
You will be prompted for your MariaDB root password and ERPNext administrator password.

## Step 11: Download ERPNext Apps

#### ERPNext 
```bash
bench get-app --branch version-15 erpnext
```
#### HRMS 
```bash
bench get-app hrms 
```
## Step 12: Install Apps on Your Site
```bash
bench --site [site-name] install-app erpnext
bench --site [site-name] install-app hrms 
```
## Step 13: Start Development Server
```bash
bench start
```
Access ERPNext at: http://[YOUR_SERVER_IP]:8000

Note: This is development mode, you must manually restart after server reboot.

## Step 14: Production Setup
#### Enable Scheduler
```bash
bench --site [site-name] enable-scheduler
```
#### Disable Maintenance Mode
```bash
bench --site [site-name] set-maintenance-mode off
```
#### Setup Production
```bash
sudo bench setup production [frappe-user]
```
#### Setup NGINX
```bash
bench setup nginx
```
#### Restart Supervisor Services
```bash
sudo supervisorctl restart all
```
>Notes
>>After production setup, ERPNext will be accessible on default HTTP/HTTPS ports >>without port number.

>Ensure firewall rules allow HTTP/HTTPS traffic.

>Use systemctl or supervisorctl to manage ERPNext services in production.

