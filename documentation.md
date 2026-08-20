# Full Installation Steps To Install Zabbix 7.4 In Ubuntu Server 26.04
So here we are guys oficially, I wont be commenting a lot just when its necessary, I have made the installation commands in order below, 
make sure that these commands assume the server is freshly installed with nothing already configured. 


### System Preparation

1) Preparing the ubuntu server

```bash
sudo apt update
```

2) Optional but recommended

```bash
sudo apt upgrade -y
```

### apache2

3) Installing the Apache2 webhost

```bash
sudo apt install -y apache2
```

4) Enabling the Apache2 webhost

```bash
sudo systemctl enable apache2
```
5) Verifying the Apache2 webhost

```bash
sudo systemctl status apache2
```

### SQL SERVER

6) Installing the SQL SERVER

```bash
sudo apt install -y mysql-server
```

7) Enabling the SQL SERVER

```bash
sudo systemctl enable --now mysql
```
8) Verifying the SQL SERVER

```bash
sudo systemctl status mysql
```

### PHP and required extensions

9) Installing PHP 8.5 && Extensions

```bash
sudo apt install -y php php-cli php-common php-mysql php-bcmath php-mbstring php-gd php-xml php-curl php-zip php-ldap libapache2-mod-php
 ```

10) Verifying the PHP Installation

```bash
php -v
 ```

11) Configuration of the PHP INI File

```bash
sudo nano /etc/php/8.5/apache2/php.ini
 ```

You can use CTRL + W to find the text inside the nano editor. We have to find the following 4 lines and ensure the values are as following:

max_execution_time = 300
max_input_time = 300
memory_limit = 138M
post_max_size = 16M
upload_max_filesize = 2M



12) Restarting the apache2

 ```bash
sudo systemctl restart apache2
```

## Requirements are done now we are going to Install the actual Zabbix

13) Get the repository

```bash
sudo wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu26.04_all.deb
```

14) Unpack the package

```bash
sudo dpkg -i zabbix-release_latest_7.4+ubuntu26.04_all.deb
```

15) Update the system to retrieve the latest packages

```bash
sudo apt update
```

16) Installing the Zabbix front-end, agent and server

```bash
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent2
```

17) Installing the additional plugins

```bash
sudo apt install zabbix-agent2-plugin-mongodb zabbix-agent2-plugin-mssql zabbix-agent2-plugin-postgresql
```

18) Setting up the initial database

```bash
sudo mysql -uroot -p
```

Note: You will enter the sql command line so I am just pasting the commands below without bash to separate them from the Linux commands:

mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;

mysql> create user zabbix@localhost identified by 'password';

mysql> grant all privileges on zabbix.* to zabbix@localhost;

mysql> set global log_bin_trust_function_creators = 1;

mysql> quit;


19) Hosting the initial database on Zabbix Server

note: Here put the password you selected in the previous part, I accidentally forgot to change password so for my my password is just password

```bash
sudo zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix
```

20) Disabling the logbin option

```bash
sudo mysql -uroot -p
```

note: Once again ahead are some sql commands so I will just paste them:

mysql> set global log_bin_trust_function_creators = 0;

mysql> quit;

21) Editing the Database password for Zabbix Server

```bash
sudo nano /etc/zabbix/zabbix_server.conf
```

Find the line DBPassword=password and change the password with anything like 1234 for example


22) Enabling the a2enmod Proxies

```bash
sudo a2enmod proxy_fcgi setenvif
```

```bash
sudo a2enconf php8.5-fpm
```

```bash
sudo a2enmod proxy proxy_http proxy_fcgi
```

23) Alright now we can finally restart all the services

```bash
sudo systemctl restart apache2 zabbix-server zabbix-agent2
```


## With all services restarted, your Zabbix server is fully operational at least I hope so.


