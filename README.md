# NGINX Server
Configuring a NGINX Reverse-Proxy.

### Installing Nginx

```
sudo apt update
sudo apt install nginx
```

### Firewall Configuration

Options avaliable:
-   Nginx Full  > HTTP (port 80) and HTTPS (port 433) are accessible.
-   Nginx HTTPS > Only HTTPS (port 433) is accessible.
-   Nginx HTTP  > Only HTTP (port 80) is accessible.

```
sudo ufw allow 'Nginx Full'
```

### Check your Web Server

```
systemctl status nginx
```

If everthing worked fine, the initial Nginx HTML will be visible on:

```
http://your_ip_address
```

### Configure Nginx
Configure Nginx to redirect for your app, based on any **domain.com**.

```json
server {
    listen      80;
    listen      [::]:80;
    server_name domain.com;

    location / {
        proxy_pass http://localhost:${app_port};
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
Here you can redirect a request for your **domain.com** to a localhost port, where could be running your app.

### License
[MIT License](./License)

