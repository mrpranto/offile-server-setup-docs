#### Install `rclone` for database manual backup
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

