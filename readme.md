Here's a professional and comprehensive `README.md` for setting up the Laravel project:

---

# **Laravel Project Setup Guide**

This guide will walk you through the process of setting up a Laravel project on your Ubuntu server using Nginx and PHP. The steps include installing required software, configuring Nginx, setting up Laravel, and ensuring everything works smoothly.

## **Table of Contents**

1. [Prerequisites](#prerequisites)
2. [Download and Extract the Project](#download-and-extract-the-project)
3. [Set Permissions](#set-permissions)
4. [Configure Nginx](#configure-nginx)
5. [Configure PHP and Composer](#configure-php-and-composer)
6. [Database Setup (Optional)](#database-setup-optional)
7. [Testing the Application](#testing-the-application)
8. [Troubleshooting](#troubleshooting)

---

## **Prerequisites**

Before proceeding, make sure you have the following installed on your Ubuntu server:

- Ubuntu 18.04 or later
- PHP 8.1 or newer
- Nginx
- MySQL or MariaDB (optional for database setup)
- Composer

To install the prerequisites, you can follow these commands:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx mysql-server php-fpm php-cli php-mysql php-mbstring php-xml php-curl php-zip unzip curl composer -y
```

---

## **Download and Extract the Project**

1. **Download the Project ZIP:**
    ```
    sudo git clone https://github.com/ritikops/Php-Laravel-setup.git
    ```
2. **Extract the Project:**

   ```bash
   sudo mv Php-Laravel-setup /var/www/
   cd /var/www/
   sudo mv Php-Laravel-setup laravel
   ```

---

## **Set Permissions**

Laravel requires certain directories to be writable by the web server user (`www-data`). Set the appropriate permissions:

```bash
sudo chown -R www-data:www-data /var/www/laravel
sudo chmod -R 775 /var/www/laravel/storage /var/www/laravel/bootstrap/cache
```

If the `storage` or `bootstrap/cache` directories are missing, create them manually:

```bash
sudo mkdir -p /var/www/laravel/storage/framework/{sessions,views,cache}
sudo mkdir -p /var/www/laravel/storage/logs
sudo mkdir -p /var/www/laravel/bootstrap/cache
```

---

## **Configure Nginx**

1. **Create an Nginx Site Configuration:**

   Create a new Nginx configuration file for the Laravel project:

   ```bash
   sudo nano /etc/nginx/sites-available/laravel
   ```

2. **Add the Following Configuration:**

   ```nginx
    server {
    listen 80;

    root /var/www/laravel-project/public;
    index index.html index.php;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ ^/index.php {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;  # Ensure this matches your PHP version
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;  # Ensure this matches your PHP version
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }

    error_log /var/log/nginx/laravel_error.log;
    access_log /var/log/nginx/laravel_access.log;
}   ```

3. **Enable the Configuration:**

   ```bash
   sudo ln -s /etc/nginx/sites-available/laravel /etc/nginx/sites-enabled/
   ```

4. **Test Nginx Configuration:**

   ```bash
   sudo nginx -t
   ```

5. **Restart Nginx:**

   ```bash
   sudo systemctl restart nginx
   ```

---

## **Configure PHP and Composer**

1. **Install Laravel Dependencies:**

   Navigate to your Laravel project directory:

   ```bash
   cd /var/www/laravel
   ```

   Then install the necessary dependencies using Composer:

   ```bash
   php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    php composer-setup.php
    php -r "unlink('composer-setup.php');"
    sudo mv composer.phar /usr/local/bin/composer

   ```

---

## **Database Setup (Optional)**

If your project requires a MySQL database, follow these steps:

1. **Create a Database and User:**

   ```bash
   sudo mysql -u root -p
   ```

   ```sql
   CREATE DATABASE laravel_db;
   CREATE USER 'laravel_user'@'localhost' IDENTIFIED BY 'password';
   GRANT ALL PRIVILEGES ON laravel_db.* TO 'laravel_user'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

2. **Update `.env` Configuration:**

   Edit the `.env` file to update the database settings:

   ```bash
   nano /var/www/laravel/.env
   ```

   Update the following lines:

   ```env
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=laravel_db
   DB_USERNAME=laravel_user
   DB_PASSWORD=password
   ```


---

## **Testing the Application**

1. **Open Your Web Browser:**

   Navigate to your server’s IP address or domain in your browser:

   ```text
   http://your_server_ip_or_domain
   ```

   You should see the Laravel welcome page.

2. **Test Routes:**

   You can test other routes such as `/hello` by going to:

   ```text
   http://your_server_ip_or_domain/hello
   ```

---

## **Troubleshooting**

- **Nginx Not Starting:**
  If you encounter issues with Nginx not starting, run the following command to check the error logs:

  ```bash
  sudo tail -f /var/log/nginx/error.log
  ```

- **Permissions Issues:**
  Ensure that the web server user (`www-data`) has the correct permissions for the `storage` and `bootstrap/cache` directories. Run the following:

  ```bash
  sudo chown -R www-data:www-data /var/www/laravel
  sudo chmod -R 775 /var/www/laravel/storage /var/www/laravel/bootstrap/cache
  ```

---

## **Conclusion**

Your Laravel project should now be successfully set up and running on your Ubuntu server with Nginx. If you encounter any issues, please refer to the troubleshooting section or consult the Laravel documentation.

---
