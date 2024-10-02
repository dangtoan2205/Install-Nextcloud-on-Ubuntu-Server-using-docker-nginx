Installing Nextcloud on Ubuntu Server 22.04 using Docker and Nginx, along with OnlyOffice, involves several steps. Here’s a detailed guide to help you set it up.
---
Prerequisites
----
1. Ubuntu Server 22.04 installed.
2. Docker and Docker Compose installed.
3. Basic knowledge of Docker and Nginx.

---
Install Nginx for Ubuntu Server
---
```
sudo apt update
sudo apt upgrade
```
1. Install Nginx
   
```
sudo apt install nginx
```

2. Start nginx and status

```
sudo systemctl start nginx
sudo systemctl status nginx
```

3. Configure Firewall
```
sudo ufw allow 'Nginx Full'
```

```
Tệp cấu hình chính của Nginx nằm ở /etc/nginx/nginx.conf, và các tệp cấu hình cho từng trang web thường nằm trong thư mục
 /etc/nginx/sites-available/. Bạn có thể tạo một tệp mới để cấu hình cho trang web của mình.
```

```
sudo systemctl restart nginx
```

---
Install Docker for Ubuntu Server
---
1. Update system
```
sudo apt update
sudo apt upgrade -y
```
2. Install Required Packages
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```
3. Add Docker’s Official GPG Key
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
4. Add Docker’s Repository
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
5. Update APT Index Again
```
sudo apt update
```
6. Install Docker Engine
```
sudo apt install docker-ce -y
```
7. Start and Enable Docker
```
sudo systemctl start docker
sudo systemctl enable docker
```
8. Verify Docker Installation
```
sudo docker --version
```
9. Manage Docker as a Non-Root User (Optional)
```
sudo usermod -aG docker $USER
```

---
Install Nextcloud
---

```
sudo snap install nextcloud
sudo nextcloud.manual-install netvn password
sudo nextcloud.occ config:system:set trusted_domains 1 --value=*
sudo nextcloud.enable-https self-signed
sudo ufw allow 80,443/tcp
```

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
      - JWT_SECRET=adminlocal123a@  # Change this

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
    client_max_body_size 10G;  # Allow larger uploads
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

---
Update file upto 10Gb (da co)
---

```
cd /etc/nginx/sites-available
sudo nano nextcloud
```

```
server {
    ...
    client_max_body_size 2G;  # Allow larger uploads
    ...
}
```

Modify PHP Configuration
If you're using PHP-FPM, you need to adjust the PHP settings for your Docker container. 
You can do this in your `Dockerfile` or by creating a custom `php.ini` file. Add or update these settings:
```
upload_max_filesize = 2G
post_max_size = 2G
memory_limit = 512M
```

Update Docker Compose (if applicable)
If you're using Docker Compose, make sure you include the environment variables in your `docker-compose.yml`:
```
services:
  app:
    ...
    environment:
      - PHP_UPLOAD_MAX_FILESIZE=2G
      - PHP_POST_MAX_SIZE=2G
```

Restart Services
If using Docker Compose
```
docker-compose down
docker-compose up -d
```

If using systemd for Nginx and PHP-FPM
```
sudo systemctl restart nginx
sudo systemctl restart php7.x-fpm  # replace with your PHP version
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
3 .Once installed, go to the settings of the OnlyOffice app and configure the Document Server URL as `http://192.168.10.26:8080`.

## Step 8: Final Configuration
Make sure to set the proper permissions for the Nextcloud data directory.
Consider enabling SSL for secure connections using Let’s Encrypt or another SSL provider.








