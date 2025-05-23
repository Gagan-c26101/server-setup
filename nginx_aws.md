# Node.js Deployment

> Steps to deploy a Node.js app to DigitalOcean using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine
I would be creating a t2.medium ubuntu machine for this demo.

## 3. Install Node and NPM
```
curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node --version

```
```

## 5. Install dependencies and test app
```
sudo npm i pm2 -g
pm2 start index

# Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

# To make sure app starts when reboot
pm2 startup ubuntu
```

## 6. Setup Firewall
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw allow from <IP_ADDRESS>
sudo ufw allow from 60.243.250.143 to any port 27017
sudo ufw deny from <IP_ADDRESS>
sudo ufw delete allow from 60.243.250.143 to any port 27017
sudo ufw delete deny from 60.243.250.143


```

## 7. Install NGINX and configure
```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default
```
Add the following to the location part of the server block
```
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8080; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }


server {
    server_name  boostbullion.com;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/boostbullion.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/boostbullion.com/privkey.pem; # managed by Certb>
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}


server {
    server_name user.boostbullion.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/user.boostbullion.com/fullchain.pem; # managed by Ce>
    ssl_certificate_key /etc/letsencrypt/live/user.boostbullion.com/privkey.pem; # managed by >
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}
```
```
# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```

## 8. Add SSL with LetsEncrypt
```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```

## 9. Install redis
```
sudo apt-get install lsb-release curl gpg
curl -fsSL https://packages.redis.io/gpg | sudo gpg --dearmor -o /usr/share/keyrings/redis-archive-keyring.gpg
sudo chmod 644 /usr/share/keyrings/redis-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/redis-archive-keyring.gpg] https://packages.redis.io/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/redis.list
sudo apt-get update
sudo apt-get install redis

sudo systemctl start redis-server
sudo systemctl enable redis-server
sudo systemctl stop redis-server

# create user on redis-server
redis-cli
ACL SETUSER newuser on >password123 allcommands allkeys
CONFIG REWRITE
redis-cli -u redis://newuser:password123@127.0.0.1:6379

# Prevent Time Out
redis-cli
CONFIG SET protected-mode no
CONFIG REWRITE

```


## 10. Install mongodb
```
sudo apt-get install gnupg curl
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
sudo systemctl daemon-reload
sudo systemctl enable mongod
sudo systemctl status mongod

dump db

mongodump --uri='mongodb://spots:$JPKasdfwe4345ttfgd5ty56jh8uuh78hw5ch@18.61.119.154:27017/admin'

restore db with name 

mongorestore --db newDbName dump/cloneDbName

```

## 11. Create New user 
```
sudo apt-get install gnupg curl
sudo adduser userName
sudo usermod -aG sudo userName

ls /home
cat /etc/passwd | grep walletapiserver

```

## 12. Install Postgresql Database and setup
```
sudo apt update && sudo apt install -y postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
sudo systemctl restart postgresql


sudo -i -u postgres

psql

CREATE DATABASE fib_db;
CREATE USER fib_user WITH PASSWORD 'fib_password';
ALTER ROLE fib_user SET client_encoding TO 'utf8';
ALTER ROLE fib_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE fib_user SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE fib_db TO fib_user;


\q

exit

sudo nano /etc/postgresql/14/main/postgresql.conf
listen_addresses = 'localhost'

echo 'export DB_HOST=localhost' >> ~/.bashrc
echo 'export DB_USER=fib_user' >> ~/.bashrc
echo 'export DB_PASSWORD=fib_password' >> ~/.bashrc
echo 'export DB_NAME=fib_db' >> ~/.bashrc
echo 'export DB_PORT=5432' >> ~/.bashrc
source ~/.bashrc


ALTER USER fib_user CREATEDB;
ALTER USER fib_user CREATEROLE;
ALTER USER fib_user SUPERUSER;
ALTER USER fib_user WITH PASSWORD 'fib_password';
GRANT ALL PRIVILEGES ON DATABASE fib_db TO fib_user;
GRANT ALL ON SCHEMA public TO fib_user;



Another Method

sudo -u postgres psql


-- Create user
CREATE USER jocky_user WITH PASSWORD 'jocky_tading';
-- Create database
CREATE DATABASE jocky_db OWNER jocky_user;
-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE jocky_db TO jocky_user;
\q  

sudo systemctl restart postgresql

for test
psql -h localhost -U jocky_user -d jocky_db




```

## 13. Run go via build  
```
cd /home/prakash/JockyTrading
go build -o jockyTradingApp main.go

sudo mv jockyTradingApp /usr/local/bin/

sudo nano /etc/systemd/system/jocky.service



[Unit]
Description=Jocky Trading Go App
After=network.target

[Service]
User=prakash
WorkingDirectory=/home/prakash/JockyTrading
ExecStart=/usr/local/bin/jockyTradingApp
Restart=always
RestartSec=5
Environment=DB_HOST=localhost
Environment=DB_USER=jocky_user
Environment=DB_PASSWORD=jocky_tading
Environment=DB_NAME=jocky_db
Environment=DB_PORT=5432

[Install]
WantedBy=multi-user.target




sudo systemctl daemon-reload
sudo systemctl start jocky.service
sudo systemctl enable jocky.service


sudo systemctl status jocky.service

sudo journalctl -u jocky.service -f

```


```
