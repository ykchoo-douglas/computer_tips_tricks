# Setup NGINX in Digital Ocean

### Step 1 - Digital Ocean

- create a Droplet
- create a DNS A record

### Step 2 - Ubuntu 20.04.4 Update and Upgrade

- login to console from Droplet
- update and upgrade the packages

```
$ sudo apt update
$ sudo apt upgrade -y
```

### Step 3 - SSH Remote Development

[ssh_susan]: https://codewithsusan.com/notes/vscode-remote-ssh
[swap_susan]: https://codewithsusan.com/notes/vscode-remote-ssh-connection-issues

1. [SSH Remote development with VSCode][ssh_susan]
2. [VSCode Remote SSH Keeps disconnecting FIXED][swap_susan]

lookup the domain to know the IP address is correct

```
$ nslookup nginx.windblue.xyz

Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   nginx.windblue.xyz
Address: 152.42.163.83
```

### Step 4 - UFW

[ufw_DO]: https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server

1. [How To Setup a Firewall with UFW on an Ubuntu and Debian Cloud Server][ufw_DO]

configure to support IPv6

```
$ sudo nano /etc/default/ufw
```

```
/etc/default/ufw
# /etc/default/ufw
#

# Set to yes to apply rules to support IPv6 (no means only IPv6 on loopback
# accepted). You will need to 'disable' and then 'enable' the firewall for
# the changes to take affect.
IPV6=yes
```

check the status, disable and enable command for UFW

```
$ sudo ufw status verbose
Status: inactive
// OR
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

$ sudo ufw disable
Output
Firewall stopped and disabled on system startup

$ sudo ufw enable
Output
Firewall is active and enabled on system startup

$ ufw app list
Available applications:
  OpenSSH
```

### Step 5 - install the NGINX

[nginx_ch1_02]: https://github.com/LinkedInLearning/learning-nginx-2492317/blob/main/Ch01/01_02-install-nginx-on-ubuntu/README.md

1. [learning-nginx-2492317_ch1_02][nginx_ch1_02]

- update the package
- install the NGINX
- check the NGINX version
- check the NGINX status

```
$ apt update
$ apt install nginx -y
$ nginx -v
$ systemctl status nginx --no-pager
```

### Step 6 - test the URL/NGINX running from browser

- enable the port 80 in firwall
- check the welcome to NGINX page is display in the browswer

```
$ ufw allow 80
$ ufw reload
```

### Step 7 - remove the NGINX welcome page

remove the link in `/etc/nginx/sites-enable`

```
$ unlink /etc/nginx/sites-enable/default
```

### Step 8 - create the site folder

[susan_nginx]: https://codewithsusan.com/notes/nginx-sites-config
[nginx_ch1_08]: https://github.com/LinkedInLearning/learning-nginx-2492317/blob/main/Ch01/01_08-add-files-to-the-root-directory/README.md

1. [Configuring sites/URLs on a Nginx web server][susan_nginx]
2. [learning-nginx-2492317_ch1_08][nginx_ch1_08]

simple web page

```
$ sudo mkdir /var/www/nginx.windblue.xyz
$ echo "<h1>This is proxy from windblue.xyz</h1>" | sudo tee /var/www/nginx.windblue.xyz/index.html
```

complex web page

```
$ cd /root
$ git clone https://github.com/LinkedInLearning/learning-nginx-2492317.git
$ mkdir /var/www/binaryville
$ tar xvf ~/learning-nginx-2492317/Binaryville_robot_website_LIL_107684.tgz --directory /var/www/binaryville/
$ ls -ltr /var/www/binaryville/
```

### Step 9 - configure the site

[nginx_ch1_07]: https://github.com/LinkedInLearning/learning-nginx-2492317/blob/main/Ch01/01_07-configure-a-virtual-host-part-2/binaryville.finished.conf
[nginx_ch2_04]: https://github.com/LinkedInLearning/learning-nginx-2492317/blob/main/Ch02/02_04-configure-logs/binaryville.conf
[susan_nginx]: https://codewithsusan.com/notes/nginx-sites-config

1. [learning-nginx-2492317_ch1_07][nginx_ch1_07]
2. [learning-nginx-2492317_ch2_04][nginx_ch2_04]
3. [Configuring sites/URLs on a Nginx web server][susan_nginx]

create the site configuration in `/etc/nginx/conf.d/nginx.windblue.xyz.conf` (simple web page)

```
$ sudo nano /etc/nginx/conf.d/nginx.windblue.xyz.conf

server {
        # Port 80 is used for incoming http requests
        listen 80 default_server;

        # The document root of our site - i.e. where its files are located
        root /var/www/nginx.windblue.xyz;

        # The URL we want this server config to apply to
        server_name nginx.windblue.xyz;

        # Which files to look for by default when accessing directories of our site
        index index.html index.htm;
}
```

create the site configuration in `/etc/nginx/conf.d/binaryville.windblue.xyz.conf` (complex web page)

```
$ sudo nano /etc/nginx/conf.d/binaryville.windblue.xyz.conf

server {
    listen 80 default_server;

    root /var/www/binaryville;

    server_name binaryville.windblue.xyz;

    index index.html index.htm index.php;

    access_log /var/log/nginx/binaryville.local.access.log;
    error_log /var/log/nginx/binaryville.local.error.log;

    location / {
        # First attempt to serve a request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /images {
        # Allow the contents of the /image folder to be listed
        autoindex on;

        access_log /var/log/nginx/binaryville.local.images.access.log;
        error_log /var/log/nginx/binaryville.local.images.error.log;
    }

    # specify the page to serve for 404 errors
    error_page 404 /404.html;

    # an exact match location for the 404 page
    location = /404.html {
        # only use this location for internal requests
        internal;
    }

    # specify the page to serve for 500 errors
    error_page 500 502 503 504 /50x.html;

    # an exact match location for the 50x page
    location = /50x.html {
        # only use this location for internal requests
        internal;
    }

    # a location to demonstrate 500 errors
    location /500 {
        fastcgi_pass unix:/this/will/fail;
    }
}
```

validate the configuration file

```
$ sudo nginx -t
```

reload the nginx to apply the new configuration

```
$ sudo systemctl reload nginx
```

check the nginx status

```
$ sudo systemctl status nginx
```

_check the web page on the web browser, still the http_

### Step 10 - install the SSL certificate

[ssl_DO]: https://www.digitalocean.com/community/tutorials/how-to-use-certbot-standalone-mode-to-retrieve-let-s-encrypt-ssl-certificates-on-ubuntu-20-04
[susan_ssl]: https://codewithsusan.com/notes/https-nginx

1. [How To Use Certbot Standalone Mode to Retrieve Let's Encrypt SSL Certificates on Ubuntu 20.04][ssl_DO]
2. [HTTPS / SSL via “Let’s Encrypt” on a Nginx Web Server][susan_ssl]

Certbot recommends using snap package for installation

```
$ sudo snap install core; sudo snap refresh core
```

remove any older version

```
$ sudo apt remove certbot
```

install the `certbot` package

```
$ sudo snap install --classic certbot
```

link the certbot command from the snap install directory to the path

```
$ sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Open up the appropriate port(s) in the firewall

```
$ sudo ufw allow 80
$ sudo ufw allow 443
```

initiate a new certificate

```
$ sudo certbot
```

_check the web page on the web browser, change to the https_

_remove certificate, replace `yourdomain.com`_

```
$ sudo certbot delete --cert-name yourdomain.com
```
