# NGINX + Node.js
Documentation of various nginx configuration and deployment.
- AWS EC2 Ubuntu 16.04

# AWS EC2 Ubuntu 16.04
- Open your AWS dashboard and create an AWS EC2 Ubuntu server.
- Leave all settings default EXCEPT for Configure Security Group.
- Under Configure Security Group, add rules: `HTTP` & `HTTPS`

## Install Node.js
- ssh into your EC2 instance
> `ssh -i YOUR_KEY.pem ubuntu@YOUR_EC2_PUBLIC_DNS_ADDRESS`

```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt install nodejs
node --version
```
## Configure firewall
```
sudo ufw enable
sudo ufw status

// port 22
sudo ufw allow ssh

// port 80
sudo ufw allow http

// port 443
sudo ufw allow https
```
## Install and configure NGINX
```
sudo apt install nginx
sudo nano /etc/nginx/sites-available/default
```
- Navigate to this section of `default.conf`; configure domain and reverse proxy.
```
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```
> Replace `yourdomain.com` & `www.yourdomain.com` with your own domain name. Change PORT to your app's PORT.

# Check NGINX config
```
sudo nginx -t
```
> return OK

# Restart NGINX; check status
```
sudo service nginx restart
sudo systemctl status nginx
```
