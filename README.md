# ngnix-wordpress

## Ngnix Setup

### Step 1: Install Nginx

#### Update your package index:

```bash
sudo apt update
```

#### Install Nginx:

```bash
sudo apt install nginx
```

#### Start Nginx:

```bash
sudo systemctl start nginx
```

#### Enable Nginx to start at boot:

```bash
sudo systemctl enable nginx
```

#### Check Nginx status:

```bash
sudo systemctl status nginx
```

You should see output that indicates Nginx is running.

### Step 2: Adjust the Firewall (Optional)

If you're using ufw (Uncomplicated Firewall), you need to allow Nginx:

#### Allow Nginx HTTP and HTTPS traffic:

```bash
sudo ufw allow 'Nginx Full'
```

#### Enable the firewall (if not already enabled):

```bash
sudo ufw enable
```

#### Check the firewall status:

```bash
sudo ufw status
```

### Step 3: Basic Configuration

Default Server Block (Optional) Nginx's default server block is located in `/etc/nginx/sites-available/default.`

#### You can edit this file to change the server's root directory, server name, and other settings:

```bash
sudo nano /etc/nginx/sites-available/default
```

Here's an example of what it might look like:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name your_domain.com www.your_domain.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Test Nginx Configuration: After making changes, test your configuration to ensure there are no syntax errors:

```bash
sudo nginx -t
```

#### Reload Nginx: If the test is successful, reload Nginx to apply the changes:

```bash
sudo systemctl reload nginx
```

### Step 4: Setting Up Additional Server Blocks (Optional)

To host multiple sites on your server, you can set up additional server blocks.

#### Create a new server block file:

```bash
sudo nano /etc/nginx/sites-available/your_domain.com
```

Add your server block configuration:

```nginx
server {
    listen 80;
    server_name your_domain.com www.your_domain.com;

    root /var/www/your_domain.com/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Enable the new server block: Link it to the sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/your_domain.com /etc/nginx/sites-enabled/
```

#### Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 5: Monitoring and Maintenance

Check Nginx logs:

- Access logs: `/var/log/nginx/access.log`
- Error logs: `/var/log/nginx/error.log`

#### Reload or Restart Nginx after changes:

```bash
sudo systemctl reload nginx
sudo systemctl restart nginx
```

This is a basic setup guide, and Nginx has many advanced features like reverse proxying, load balancing, caching, etc., that you can explore as needed.

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

### Step 2: Install PHP-FPM and Resolve Issues

#### 1. Install PHP-FPM

Since php-fpm is not found, you need to install PHP-FPM for PHP 8.1. Use the following commands to install it:

```bash
sudo apt-get update
sudo apt-get install php8.1-fpm
```

#### 2. Verify PHP-FPM Installation

After installation, verify that php-fpm is available:

```bash
php-fpm8.1 -v
```

If this works, then PHP-FPM for PHP 8.1 is installed and running. If the command still isn’t found, ensure that PHP-FPM binaries are in your PATH.

#### 3. Check PHP-FPM Service Status

Check if the PHP-FPM service is running:

```bash
sudo systemctl status php8.1-fpm
```

If it’s not running, start the service:

```bash
sudo systemctl start php8.1-fpm
```

And ensure it starts on boot:

```bash
sudo systemctl enable php8.1-fpm
```

### Step 3: Test PHP Configuration

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

### Step 2: Restart Nginx

After installing the extension, restart Apache to load the new PHP configuration:

```bash
sudo systemctl restart nginx
```

### Step 3: MySQL fpm

```bash
sudo apt-get install php8.1-mysqli
```

For curl, if you see warnings about it being already loaded, it should be fine as long as there are no further issues.

Check PHP Configuration Files:

Look for any configuration files that might be incorrectly loading extensions or causing conflicts. Check the configuration files in:

```bash
/etc/php/8.1/cli/conf.d/
/etc/php/8.1/fpm/conf.d/
```

Ensure there are no duplicate or conflicting extension lines.

### Step 4: Restart Services:

After making changes, restart the PHP-FPM and Nginx services:

```bash
sudo systemctl restart php8.1-fpm
sudo systemctl restart nginx
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
CREATE USER 'azuredeploy'@'localhost' IDENTIFIED BY 'azuredeploy';
GRANT ALL PRIVILEGES ON wp_db.* TO 'azuredeploy'@'localhost';
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
SHOW GRANTS FOR 'azuredeploy'@'localhost';
```

### 5. Create Database

```sql
CREATE DATABASE wp_db;
```

### 6. Verify Database Name

Ensure the database exists:

```sql
SHOW DATABASES;
```

If necessary, grant the appropriate privileges:

```sql
GRANT ALL PRIVILEGES ON wp_db.* TO 'azuredeploy'@'localhost';
FLUSH PRIVILEGES;
```

Following these steps must make run your wordpress site with apache server and mysql database at specified domain.
