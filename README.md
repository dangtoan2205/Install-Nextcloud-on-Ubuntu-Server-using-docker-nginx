Installing Nextcloud on Ubuntu Server 22.04 using Docker and Nginx, along with OnlyOffice, involves several steps. Here’s a detailed guide to help you set it up.
---
Prerequisites
----
1. Ubuntu Server 22.04 installed.
2. Docker and Docker Compose installed.
3. Basic knowledge of Docker and Nginx.

## Step 1: Install Docker and Docker Compose
If you haven’t already installed Docker and Docker Compose, you can do so by running:
```
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose
```

## Step 2: Create Docker Compose File
Create a directory for your Nextcloud setup:
```
mkdir ~/nextcloud && cd ~/nextcloud
```

Then create a `docker-compose.yml` file:
```
version: '3.8'

services:
  nextcloud:
    image: nextcloud
    restart: always
    ports:
      - "8080:80"
    volumes:
      - nextcloud_data:/var/www/html
    environment:
      MYSQL_PASSWORD: password
      MYSQL_USER: nextcloud
      MYSQL_DATABASE: nextcloud
    depends_on:
      - db

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_PASSWORD: password
      MYSQL_USER: nextcloud
      MYSQL_DATABASE: nextcloud
    volumes:
      - db_data:/var/lib/mysql

  onlyoffice:
    image: onlyoffice/documentserver
    restart: always
    ports:
      - "81:80"
    environment:
      - JWT_SECRET=your_secret_key  # Change this

volumes:
  nextcloud_data:
  db_data:
```

## Step 3: Start Docker Containers
Run the following command to start the containers:
```
sudo docker-compose up -d
```

## Step 4: Configure Nginx
Install Nginx if you haven't already:
```
sudo apt install -y nginx
```

Create a new Nginx configuration file for Nextcloud:
```
sudo nano /etc/nginx/sites-available/nextcloud
```

Add the following configuration:
```
server {
    listen 80;
    server_name 192.168.26.10;  # Change this to your Nextcloud IP

    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    listen 80;
    server_name 192.168.10.26;  # Change this to your OnlyOffice IP

    location / {
        proxy_pass http://localhost:81;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## Step 5: Enable the Nginx Configuration
Enable the new configuration and restart Nginx:
```
sudo ln -s /etc/nginx/sites-available/nextcloud /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Step 6: Access Nextcloud
Open a web browser and navigate to `http://192.168.26.10`. You should see the Nextcloud setup page. Follow the on-screen instructions to complete the setup.

## Step 7: Configure OnlyOffice in Nextcloud
After setting up Nextcloud, you need to integrate OnlyOffice. Here’s how:

1. Go to the Nextcloud apps section.
2. Search for and install the "OnlyOffice" app.
3 .Once installed, go to the settings of the OnlyOffice app and configure the Document Server URL as `http://192.168.10.26`.

## Step 8: Final Configuration
Make sure to set the proper permissions for the Nextcloud data directory.
Consider enabling SSL for secure connections using Let’s Encrypt or another SSL provider.








