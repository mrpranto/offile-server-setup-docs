
## Offline app setup full process step by step

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

- Clone git project api repository:
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
> `git clone --depth 1 --project-url--`
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

- Login to your mysql server and add an user 
>`mysql -u root -p `
>`CREATE USER 'replica'@'%' IDENTIFIED BY 'Replica@123';`
>`GRANT ALL ON *.* TO 'replica'@'%';`

- Try to login on mysql server remotly through this command
>`mysql -u replica -pReplica@123 -h serveo.net -P 3360`

- Try to login on server remotly through this command
> `ssh -p 3333 familymart@serveo.net`
> `@reboot ssh -f -N -R 3333:localhost:22 serveo.net`
> `@reboot ssh -f -N -R 3360:localhost:3306 serveo.net`

#### Setp 8: Setup mysql master slave replication on server database
- Set master database configration
>`sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf`
>`# bind-address          = 127.0.0.1`

- Uncomment this line or add this 
> `server-id              = 1`
> `log_bin                 = /var/log/mysql/mysql-bin.log`

- Add database name on this
>`binlog_do_db            = [database_name]`

- Restart mysql server
>`sudo service mysql restart`

- Login on mysql master server
> `mysql -u [root] -p[password]`
> `ALTER USER 'replica'@'%' IDENTIFIED WITH mysql_native_password BY 'Replica@123';`
> `FLUSH PRIVILEGES;`
> `SHOW MASTER STATUS;`
> `Copy the table`

- Login to mysql slave server
> `mysql -u [root] -p[password]`
> `Upload master database on slave server`
> `STOP SLAVE;`

    CHANGE MASTER TO
    MASTER_HOST='serveo.net',
    MASTER_PORT=3360,
    MASTER_USER='[MASTER_REPLICA_USER]',
    MASTER_PASSWORD='[MASTER_REPLICA_USER]',
    MASTER_LOG_FILE='[File - mysql-bin.000001]',
    MASTER_LOG_POS=[File POSITION];

>`SET GLOBAL sql_slave_skip_counter = 1;`
>`START SLAVE;`
>`SHOW SLAVE STATUS\G`

- `crontab -e` All command list, Please check all this information is added or not.
> `* * * * * cd /var/www/html/pos-api && php artisan queue:work --sleep=3 --tries=3 >> /dev/null 2>&1`
> `* * * * * cd /var/www/html/pos-api && php artisan schedule:run >> /dev/null 2>&1`
> `@reboot ssh -f -N -R 3333:localhost:22 serveo.net`
> `@reboot ssh -f -N -R 3360:localhost:3306 serveo.net`

- Use this process for run always ssh and mysql tunnel
- For SSH
> `sudo nano /etc/systemd/system/ssh-tunnel.service`

       [Unit]
       Description=SSH Tunnel to Serveo
       After=network.target
       
       [Service]
       ExecStart=/usr/bin/ssh -f -N -R 3333:localhost:22 serveo.net
       Restart=always
       User=familymart
       
       [Install]
       WantedBy=multi-user.target

>`sudo systemctl enable ssh-tunnel`
>`sudo systemctl start ssh-tunnel`

- For MYSQL
> `sudo nano /etc/systemd/system/ssh-mysql-tunnel.service`

        [Unit]
       Description=SSH Tunnel to Serveo
       After=network.target
       
       [Service]
       ExecStart=/usr/bin/ssh -f -N -R 3360:localhost:3306 serveo.net
       Restart=always
       User=familymart
       
       [Install]
       WantedBy=multi-user.target

>`sudo systemctl enable ssh-mysql-tunnel`
>`sudo systemctl start ssh-mysql-tunnel`


#### Setp 9: Install redis 
>`sudo apt install redis-server`
>`sudo systemctl start redis-server`
>`sudo systemctl enable redis-server`
>`sudo systemctl status redis-server`
>`redis-cli ping`

#### Step 10: Install avahi-daemon for accessing server by server name 
> `sudo apt-get install avahi-daemon`
#### Step 11: Install `rclone` for database manual backup
> `sudo apt update`
> `sudo apt install rclone`

1.  **Start the Configuration Process:**
    
    bash
    
    Copy code
    
    `rclone config`
    
2.  **Create a New Remote:**
    
    *   Type `n` to create a new remote and press `Enter`.
    *   You'll be asked to name the remote. You can call it something like `mydrive`.
3.  **Select Google Drive as the Storage Type:**
    
    *   When prompted to choose a storage type, type `drive` and press `Enter`.
4.  **Client ID and Secret:**
    
    *   You can leave `client_id` and `client_secret` empty by pressing `Enter` for each. If you want to set up your own, you can create these in the Google Cloud Console.
5.  **Set Scope:**
    
    *   Choose `1` for full access.
6.  **Advanced Configuration:**
    
    *   When asked about advanced config, you can generally choose `n` unless you have specific needs.
7.  **Auto Configuration:**
    
    *   Choose `y` for auto-config. Since you're on a server without a browser, this may fail, and you'll be prompted to follow a manual process.
    *   If it fails, `rclone` will provide you with a URL. Copy this URL.
8.  **Authorize `rclone`:**

    *   run this command in your local pc `ssh -L 53682:localhost:53682 username@your_server_ip`
    *   Paste the URL into a browser on a device with access to your Google account. Follow the authorization steps.
    *   After authorization, you'll receive a verification code. Copy this code.
9.  **Enter Verification Code:**
    
    *   Return to your server, and paste the verification code into the terminal.
10.  **Team Drive:**

     * If you're using a personal Google Drive, choose `n` for the team drive option.
    
12.  **Confirm Configuration:**

     *   `rclone` will display the details of your new remote. Type `y` to confirm and save the configuration.
13.  **Exit:**

     *  Once everything is set up, type `q` to quit the `rclone` config.
   
14. **Now you can upload file to google drive:**
    * `rclone copy /var/www/html/backup/familymart.sql  mydrive:/ReformedTech/Familymart/$(date +'%Y-%m-%d') --update`
      
15. **Set on cron tab in every 6 hours: **
    * `0 */6 * * * mysqldump -u [database_name] -p[pasword] [database_name] > /var/www/html/backup/[database_name] && rclone copy /var/www/html/backup/[database_name] mydrive:/ReformedTech/[client_name]/$(date +\%Y-\%m-\%d) --update`


