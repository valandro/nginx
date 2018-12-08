[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

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
Configure Nginx to redirect for your app, based on any `domain.com`.

```
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
Here you can redirect a request for your `domain.com` to a localhost port, where could be running your app.

### Cerbot
Certbot is an easy-to-use automatic client that fetches and deploys SSL/TLS certificates for your web server. Certbot was developed by EFF and others as a client for Let’s Encrypt and was previously known as “the official Let’s Encrypt client” or “the Let’s Encrypt Python client.” Certbot will also work with any other CAs that support the ACME protocol.

### Generate a HTTPS certificate

#### Install 
Installing the Cerbot on your machine.

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python-certbot-nginx 
``` 
#### Get Started
Running this command, Cerbot will generate a certificate for you, and also automatically edit your nginx configuration.

```
sudo certbot --nginx
```

After that, your `.conf` file will have these new lines:

```
server {
    listen      443;
    listen      [::]:443;
    server_name domain.com;
    ## Cerbot Certificate
    ssl_certificate /etc/letsencrypt/live/domain.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/domain.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

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

Now you have your `HTTPS certificate` and your port 443 is enable on your nginx `.conf`.

#### Note
Check if your por 443 is enable on your firewall configuration.

```
sudo ufw status
```

### Automating renewal
The Certbot packages on your system come with a `cron job` that will renew your certificates automatically before they expire. Since Let's Encrypt certificates last for 90 days, it's highly advisable to take advantage of this feature. You can test automatic renewal for your certificates by running this command:

```
sudo certbot renew --dry-run
```

More detailed information and options about renewal can be found in the [full documentation](https://certbot.eff.org/docs/using.html#renewal).

### Redirect HTTP to HTTPS

It's simple, just add this on you `.conf` file.

```
server {
    listen      80;
    listen      [::]:80;
    server_name www.domain.com domain.com.br;
    return 301 https://domain.com$request_uri; # managed by Certbot

}
```

### License
[MIT License](LICENSE)
