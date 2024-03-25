Step 1: Install Nginx
If Nginx isn't already installed, you can install it using apt:

bash
Copy code
sudo apt update
sudo apt install nginx
Step 2: Create SSL Certificate and Key
If you don't have SSL certificates, you can create self-signed ones for testing purposes:

bash
Copy code
sudo mkdir -p /etc/ssl/cmsindonesia.com
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/cmsindonesia.com/cmsindonesia.com.key -out /etc/ssl/cmsindonesia.com/cmsindonesia.com.pem
Follow the prompts to fill in your certificate information.

Step 3: Create Nginx Configuration Files
Create configuration files for each of your server blocks:

bash
Copy code
sudo nano /etc/nginx/sites-available/cmsindonesia.com
Paste the following configuration into the file:

nginx
Copy code
server {
    listen 80;
    server_name cmsindonesia.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name cmsindonesia.com;

    ssl_certificate /etc/ssl/cmsindonesia.com/cmsindonesia.com.pem;
    ssl_certificate_key /etc/ssl/cmsindonesia.com/cmsindonesia.com.key;

    root /usr/share/nginx/html;
    index index.html;

    location / {
        proxy_pass http://127.0.0.1:4105;
        add_header Cache-Control "no-cache, no-store, must-revalidate";
    }
}
Create the configuration file for the admin.cmsindonesia.com server block:

bash
Copy code
sudo nano /etc/nginx/sites-available/admin.cmsindonesia.com
Paste the following configuration into the file:

nginx
Copy code
server {
    listen 80;
    server_name admin.cmsindonesia.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;

    server_name admin.cmsindonesia.com;

    ssl_certificate /etc/ssl/cmsindonesia.com/cmsindonesia.com.pem;
    ssl_certificate_key /etc/ssl/cmsindonesia.com/cmsindonesia.com.key;

    location / {
        proxy_pass http://127.0.0.1:4205;
        add_header Cache-Control "no-cache, no-store, must-revalidate";
    }
}
Step 4: Enable Configuration Files
Create symbolic links from the sites-available directory to the sites-enabled directory:

bash
Copy code
sudo ln -s /etc/nginx/sites-available/cmsindonesia.com /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/admin.cmsindonesia.com /etc/nginx/sites-enabled/
Step 5: Test Configuration and Restart Nginx
Test the Nginx configuration for syntax errors:

bash
Copy code
sudo nginx -t
If the test is successful, restart Nginx:

bash
Copy code
sudo systemctl restart nginx
Step 6: Adjust Firewall (if necessary)
If you have a firewall enabled (like UFW), allow HTTP and HTTPS traffic:

bash
Copy code
sudo ufw allow 'Nginx Full'
Now, Nginx should be configured to serve your sites cmsindonesia.com and admin.cmsindonesia.com on HTTP and HTTPS. Make sure your DNS records are properly configured to point to your server's IP address.
