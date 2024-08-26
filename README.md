# apache-wordpress

wordpress with apache server and mysql database at specified domain

## Apache Installation

### Step 1: Install Apache

```bash
sudo apt update
sudo apt install apache2
```

### Step 2: Verify the Installation

After installing Apache, you can verify that the service is running by typing:

```bash
sudo systemctl status apache2
```

You should see output similar to the following:

```bash
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-06-21 15:00:00 UTC; 1min 30s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 12345 (apache2)
      Tasks: 55 (limit: 1137)
     Memory: 6.0M
     CGroup: /system.slice/apache2.service
             ├─12345 /usr/sbin/apache2 -k start
             ├─12346 /usr/sbin/apache2 -k start
             └─12347 /usr/sbin/apache2 -k start
```

The output shows that Apache is active and running. The "active (running)" part of the output indicates that Apache is running. If Apache is not running, you can start it using:

```bash
sudo systemctl start apache2
```

### Step 3: Configure Apache to Start on Boot

To ensure that Apache starts automatically when your server boots, you can enable the service using the following command:

```bash
sudo systemctl enable apache2
```

## Configure Apache

To configure Apache to serve a website based on a domain name, you'll need to set up a Virtual Host. Here's how you can do it on Ubuntu:

### Step 1: Enable the rewrite module (optional but recommended)

This module allows URL rewriting, which is often needed for domain-based routing.

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 2: Create a Virtual Host Configuration File

#### Navigate to the sites-available directory:

```bash
cd /etc/apache2/sites-available/
```

#### Create a new configuration file for your domain:

- #### Replace yourdomain.com with your actual domain name.

```bash
sudo nano yourdomain.com.conf
```

- #### Add the following content to the file:

```apache
<VirtualHost \*:80>
ServerAdmin admin@yourdomain.com
ServerName yourdomain.com
ServerAlias www.yourdomain.com

    DocumentRoot /var/www/yourdomain.com

    <Directory /var/www/yourdomain.com>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/yourdomain.com_error.log
    CustomLog ${APACHE_LOG_DIR}/yourdomain.com_access.log combined

</VirtualHost>
```

- ServerAdmin: Your email address for administrative purposes.
- ServerName: The primary domain name.
- ServerAlias: Additional domain names (e.g., www.yourdomain.com).
- DocumentRoot: The directory where your website files are located.

### Step 3: Create the Document Root Directory

#### Create the directory:

```bash
sudo mkdir -p /var/www/yourdomain.com
```

#### Set the appropriate permissions:

```bash
sudo chown -R $USER:$USER /var/www/yourdomain.com
sudo chmod -R 755 /var/www/yourdomain.com
```

#### Add an index.html file (for testing):

```bash
echo "<html><h1>Welcome to yourdomain.com</h1></html>" | sudo tee /var/www/yourdomain.com/index.html
```

### Step 4: Enable the Virtual Host Configuration

#### Enable the new virtual host:

```bash
sudo a2ensite yourdomain.com.conf
```

#### Disable the default virtual host (optional but recommended):

```bash
sudo a2dissite 000-default.conf
```

#### Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

### Step 5: Update DNS Records

Make sure your domain's DNS records point to your server's IP address. This is done through your domain registrar's control panel.

### Step 6: Test the Configuration

Open a web browser and go to http://yourdomain.com. You should see the "Welcome to yourdomain.com" message you added in the index.html file.
If you need to use SSL (HTTPS), you can obtain and configure an SSL certificate using Let's Encrypt with Certbot. This is often required for modern web standards.

## Wordpress Setup

```bash
git clone --depth 1 <repository_url> <destination_directory>
```

- <repository_url> - this repository_url
- <destination_directory> - yourdomain folder

If Apache is showing PHP code instead of executing it, it's likely that the PHP module isn't enabled or PHP isn't properly configured in Apache. Here's how you can resolve this issue:

### Step 1: Install PHP (if not already installed)

First, make sure PHP is installed on your system:

```bash
sudo apt update
sudo apt install php libapache2-mod-php
```

### Step 2: Enable the PHP module in Apache

After installing PHP, you need to enable the PHP module in Apache:

Enable the Correct PHP Module
After installing, enable the appropriate PHP module:

```bash
sudo a2enmod php7.x
```

Replace 7.x with your specific PHP version. You can find out which version of PHP is installed using:

```bash
php -v
```

For example, if you have PHP 7.4 installed:

```bash
sudo a2enmod php7.4
```

### Step 4: Restart Apache

Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

### Step 5: Test PHP Configuration

Create a test PHP file as mentioned earlier:

```bash
echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/phpinfo.php
```

Visit http://your_server_ip/phpinfo.php in your browser to confirm that PHP is working.

If You Have Multiple PHP Versions Installed
If you have multiple PHP versions installed and want to switch between them, you can use:

```bash
sudo update-alternatives --config php
```

This command lets you choose the default PHP version on your system. After selecting a version, re-enable the corresponding Apache module as described above and restart Apache.

## Install MySQL

You will need setup mysql database to run wordpress properly

### Step 1: Install the MySQLi Extension

You can install the MySQLi extension for PHP using the following command:

```bash
sudo apt update
sudo apt install php-mysql
```

This command will install the MySQLi extension, which is bundled with the php-mysql package.

### Step 2: Restart Apache

After installing the extension, restart Apache to load the new PHP configuration:

```bash
sudo systemctl restart apache2
```

#### Go to php.ini

```bash
sudo nano /etc/php/7.x/cli/php.ini
```

- Search for `extension=mysqli`

if there is `;` in front of `extension=mysqli`
remove `;` to enable mysqli extension

#### Save and exit the editor (press CTRL + X, then Y, and Enter).

### Step 3: Verify the Installation

To verify that the MySQLi extension is enabled, you can create a PHP file that calls phpinfo():

#### Create a new file:

```bash
sudo nano /var/www/html/phpinfo.php
```

#### Add the following content to the file:

```php
<?php phpinfo(); ?>
```

#### Save and exit the editor (press CTRL + X, then Y, and Enter).

Open your browser and navigate to http://your_server_ip/phpinfo.php.

Search for "mysqli" on the page. If you see information about the MySQLi extension, it means the extension is correctly installed and enabled.

### Step 4: Remove the phpinfo.php File (Security Best Practice)

Once you've verified that everything is working, it's a good idea to delete the phpinfo.php file to prevent exposing sensitive information about your server configuration:

```bash
sudo rm /var/www/html/phpinfo.php
```

## Configuring Database

Lets create database , username , password of mysql to use in wordpress

### 1. Check Database Credentials in wp-config.php

#### Ensure that the database credentials in your wp-config.php file are correct. Open the wp-config.php file:

```bash
sudo nano /var/www/html/wp-config.php
```

#### Verify the following settings:

```php

/** The name of the database for WordPress */
define('DB_NAME', 'your_database_name');

/** MySQL database username */
define('DB_USER', 'your_database_user');

/** MySQL database password */
define('DB_PASSWORD', 'your_database_password');

/** MySQL hostname */
define('DB_HOST', 'localhost');
```

- DB_NAME: The name of your WordPress database.
- DB_USER: The MySQL username with access to the database.
- DB_PASSWORD: The password for the MySQL user.
- DB_HOST: Typically localhost, but could be different if your database is on a remote server.

### 2. Verify Database Connection

#### You can test the database connection using the MySQL command line:

```bash
mysql -u your_database_user -p -h localhost
```

Replace your_database_user with your MySQL username. After entering the password, you should be able to access the MySQL prompt if the credentials are correct.

### 3. Check MySQL Server Status

Ensure that the MySQL server is running:

```bash
sudo systemctl status mysql
```

If MySQL is not running, start it with:

```bash
sudo systemctl start mysql
```

Create a New MySQL User and Database

```bash
sudo mysql -u root -p
```

```sql
CREATE USER 'your_database_user'@'localhost' IDENTIFIED BY 'your_database_password';
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_database_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 4. Verify MySQL User Privileges

Ensure that the MySQL user has the correct privileges for the database:

Log in to MySQL as the root user:

```bash
sudo mysql -u root -p
```

Check user privileges:

```sql
SHOW GRANTS FOR 'your_database_user'@'localhost';
```

### 5. Verify Database Name

Ensure the database exists:

```sql
SHOW DATABASES;
```

If necessary, grant the appropriate privileges:

```sql
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_database_user'@'localhost' IDENTIFIED BY 'your_database_password';
FLUSH PRIVILEGES;
```

Following these steps must make run your wordpress site with apache server and mysql database at specified domain.
