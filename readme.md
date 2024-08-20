﻿## Offline app setup full process step by step

### *First need to install a fresh ubuntu server install on a server pc*.

#### Setp 1: Install apache server 

 - Update ubuntu all packages:
>  `sudo apt update`

- Install apache2:
> `sudo apt install apache2`

#### Setp 2: Install PHP 8.1 ^

- Intall php with required packages:
> `sudo apt install software-properties-common`
> `sudo add-apt-repository ppa:ondrej/php`
> `sudo apt update`
> `sudo apt install php8.1`
- Install PHP 8.1 extensions required for Laravel:
>`sudo apt install php8.1-cli php8.1-common php8.1-mysql php8.1-pgsql php8.1-sqlite3 php8.1-xml php8.1-mbstring php8.1-curl php8.1-zip php8.1-bcmath php8.1-intl php8.1-gd php8.1-soap php8.1-xmlrpc php8.1-imagick php8.1-memcached php8.1-redis
`

- Install Composer:
>`sudo apt install curl`
>`curl -sS https://getcomposer.org/installer | php`
>`sudo mv composer.phar /usr/local/bin/composer`

#### Step 3: Install Mysql server and mysql client

- Update the package list:
> `sudo apt update`

- Install MySQL Server:
> `sudo apt install mysql-server`

- Run mysql and add root user and password:
> `sudo mysql`
> `CREATE USER 'root' IDENTIFIED WITH mysql_native_password BY 'password';`
> `GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION;`
> `FLUSH PRIVILEGES;`
>`exit`
- Secure MySQL Installation:
> `sudo mysql_secure_installation`

#### Step 4: Install Laravel or any PHP project

- Allow full permissionn:
> `sudo chmod -R 777 /var/www`

- Install git:
> `sudo apt install git-all`

- Clone git project repository:
>`git clone --project--url--`

- Make a .env file on root path
> `sudo nano .env`
> `composer update`

- Upload your existing database if you have, other wise you can run:
>`php artisan migrate`
>`php artisan db:seed --class=LiveAppSeeder`
>`php artisan passport:install`
>`Allow /storage all folder permission 777 also bootstarp/cache`

- Enable site url:
> `sudo nano /etc/apache2/sites-available/[project-folder].conf`


    <VirtualHost *:8000>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/pos-api/public
    ServerName localhost

    <Directory /var/www/html/pos-api/public>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error8000.log
    CustomLog ${APACHE_LOG_DIR}/access8000.log combined
</VirtualHost>

- Open port 8000
> `sudo nano /etc/apache2/ports.conf`
> `Listen 8000`
> `sudo a2ensite [project-folder-name].conf`
> `sudo a2enmod rewrite`
> `sudo systemctl restart apache2`

#### Step 5: Install NVM and Node js 16.17.0

>`sudo apt update`
>`sudo apt install build-essential libssl-dev curl -y`
>`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash`
>`export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm`

>`nvm install 16.17.0`
>`nvm use 16.17.0`

#### Step 6: Install NextJs Project

- Clone git project repository
> `git colen --project-url--`
> `npm install`
> `sudo nano .env`

    NEXT_PUBLIC_API_URL=http://192.168.0.200:8000/api/v1

    NEXTAUTH_URL=http://192.168.0.200:8000

    NODE_TLS_REJECT_UNAUTHORIZED="1"
- Change default project root path
>`sudo nano /etc/apache2/sites-available/000-default.conf`
> `change document root path to your prject folder path`
> `sudo systemctl restart apache2`

#### Setp 7: Open server access on internet
- Run this command to open server tunel and also add on crontab -e
>`ssh -f -N -R 3333:localhost:22 serveo.net`
>

- Run this command to open mysql server tunel and also add on crontab -e
>`ssh -f -N -R 3360:localhost:3306 serveo.net`
