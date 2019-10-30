# NGINX + Node.js
Documentation of various nginx configuration and deployment.
Supported:
- AWS EC2 Ubuntu 16.04

# AWS EC2 Ubuntu 16.04
- Open your AWS dashboard and create an AWS EC2 Ubuntu server.
- Leave all settings default EXCEPT for Configure Security Group.
- Under Configure Security Group, add rules: `HTTP` & `HTTPS`

- Create and download a new key pair to SSH into EC2 instance.
- Navigate to your key pair file location and run:
```
chmod 400 ./YOUR_KEY_PAIR_NAME.pem
```
- SSH into your EC2 instance
```
ssh -i YOUR_KEY_PAIR.pem ubuntu@YOUR_EC2_PUBLIC_DNS_ADDRESS
```

- Update the server
```
sudo apt-get update && sudo apt-get upgrade -y
```
### Install Node.js
```
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install -y nodejs
node --version
```
### Install PM2 - process manager for your Node.js app
```
sudo npm i pm2 -g
pm2 start YOUR_APP_FILENAME

# Other pm2 commands
pm2 show server
pm2 status YOUR_APP_FILENAME
pm2 restart YOUR_APP_FILENAME
pm2 stop YOUR_APP_FILENAME

(Show log stream)
pm2 logs

(Clear logs)
pm2 flush

# To make sure app starts when reboot
pm2 startup ubuntu
```
### Configure firewall
```
sudo ufw enable
sudo ufw status

# port 22
sudo ufw allow ssh

# port 80
sudo ufw allow http

# port 443
sudo ufw allow https
```
### Install and configure NGINX
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
> Change PORT to your app's PORT. Optional: replace `yourdomain.com` & `www.yourdomain.com` with your own domain name. 

### Check NGINX config
```
sudo nginx -t
```
> return OK

### Restart NGINX and ensure running - done
```
sudo service nginx restart
sudo systemctl status nginx
```
### Common commands
```
# check nginx status
sudo systemctl status nginx

# start nginx
sudo systemctl start nginx

# start nginx on startup
sudo systemctl enable nginx

# reload nginx
sudo systemctl reload nginx

# restart nginx
sudo service nginx restart

# show firewall status
sudo ufw status

```
