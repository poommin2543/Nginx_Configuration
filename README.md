# Nginx Configuration Guide

## Step 1 – Installing Nginx

```sh
sudo apt update
sudo apt install nginx
```

## Step 2 – Adjusting the Firewall

```sh
sudo ufw app list
```

### Expected Output:
```
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
  OpenSSH
```

Enable Nginx HTTP:

```sh
sudo ufw allow 'Nginx HTTP'
```

Verify the change:

```sh
sudo ufw status
```

### Expected Output:
```
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)
```

## Step 3 – Checking your Web Server

```sh
sudo systemctl status nginx
```

### Expected Output:
```
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2020-04-20 16:08:19 UTC; 3 days ago
     Docs: man:nginx(8)
 Main PID: 2369 (nginx)
    Tasks: 2 (limit: 1153)
   Memory: 3.5M
   CGroup: /system.slice/nginx.service
           ├─2369 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
           └─2380 nginx: worker process
```

## Step 4 – Managing the Nginx Process

- **Stop Nginx:**
  ```sh
  sudo systemctl stop nginx
  ```
- **Start Nginx:**
  ```sh
  sudo systemctl start nginx
  ```
- **Restart Nginx:**
  ```sh
  sudo systemctl restart nginx
  ```
- **Reload configuration:**
  ```sh
  sudo systemctl reload nginx
  ```
- **Disable auto-start on boot:**
  ```sh
  sudo systemctl disable nginx
  ```
- **Enable auto-start on boot:**
  ```sh
  sudo systemctl enable nginx
  ```

## Step 5 – Setup Nginx Server

### MQTT Configuration

```sh
cd /etc/nginx/sites-available
sudo nano /etc/nginx/sites-available/mqtt
```

**Set Configuration:**
```nginx
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}

server {
    listen 80;
    server_name mqtt.noom.website;

    location /mqtt {
        proxy_pass http://127.0.0.1:9001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_set_header Host $host;
    }
}
```

Enable Configuration:
```sh
sudo ln -s /etc/nginx/sites-available/mqtt /etc/nginx/sites-enabled/
```

### Janus Configuration

```sh
cd /etc/nginx/sites-available
sudo nano /etc/nginx/sites-available/janus
```

Enable Configuration:
```sh
sudo ln -s /etc/nginx/sites-available/janus /etc/nginx/sites-enabled/
```

### Hostname Configuration

```sh
cd /etc/nginx/sites-available
sudo nano /etc/nginx/sites-available/webrover
```

**Set Configuration:**
```nginx
server {
    listen      80;
    server_name rover.noom.website;
    charset utf-8;
    root    /var/www/distrover;
    index   index.html;
    
    location / {
        root /var/www/distrover;
        try_files $uri  /index.html;
    }
    
    error_log  /var/log/nginx/vue-app-error.log;
    access_log /var/log/nginx/vue-app-access.log;
}
```

Enable Configuration:
```sh
sudo ln -s /etc/nginx/sites-available/webrover /etc/nginx/sites-enabled/
```

### Checking for Syntax Errors

```sh
sudo nginx -t
```

### Deploying the Application

Copy the `dist` folder to the web server directory:
```sh
cp -r dist /var/www
```

Restart Nginx:
```sh
sudo systemctl restart nginx
```

---

This guide provides a step-by-step process to install, configure, and manage Nginx with different services and domains.

