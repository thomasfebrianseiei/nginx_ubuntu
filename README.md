# Setting Up Nginx for cmsindonesia.com

Follow these steps to set up Nginx for serving `cmsindonesia.com` and `admin.cmsindonesia.com`:

### Step 1: Install Nginx

If Nginx isn't already installed, you can install it using apt:

```bash
sudo apt update
sudo apt install nginx
Step 2: Create SSL Certificate and Key
Step 2.1: Create SSL Directory
bash
Copy code
sudo mkdir -p /etc/ssl/cmsindonesia.com
Step 2.2: Generate SSL Certificate and Key
Run the following command to generate a self-signed SSL certificate and key:

bash
Copy code
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/cmsindonesia.com/cmsindonesia.com.key -out /etc/ssl/cmsindonesia.com/cmsindonesia.com.pem
Follow the prompts to fill in the required information. This will create a self-signed SSL certificate (cmsindonesia.com.pem) and key (cmsindonesia.com.key) valid for 365 days.

Step 2.3: Set Permissions (Optional)
Optionally, you can set the appropriate permissions for the SSL directory and files:

bash
Copy code
sudo chmod -R 600 /etc/ssl/cmsindonesia.com
This will ensure that only the owner has read and write permissions for the SSL files.

Step 3: Create Nginx Configuration Files
Step 3.1: Create Configuration File for cmsindonesia.com
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
Step 3.2: Create Configuration File for admin.cmsindonesia.com
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
