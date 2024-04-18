# Setup Mosquitto in Digital Ocean

### Step 1 - Droplet and DNS A record

### Step 2 - SSH Remote Development

[ssh_susan]: https://codewithsusan.com/notes/vscode-remote-ssh
[swap_susan]: https://codewithsusan.com/notes/vscode-remote-ssh-connection-issues

- [SSH Remote development with VSCode][ssh_susan]
- [VSCode Remote SSH Keeps disconnecting FIXED][swap_susan]

lookup the domain to know the IP address is correct

```
$ nslookup mqtt.windblue.xyz

Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   mqtt.windblue.xyz
Address: 152.42.182.200
```

### Step 3 - Update and Upgrade

update and upgrade the packages

```
$ sudo apt update
$ sudo apt upgrade
```

### Step 4 - UFW

[ufw_DO]: https://www.digitalocean.com/community/tutorials/how-to-setup-a-firewall-with-ufw-on-an-ubuntu-and-debian-cloud-server

- [How To Setup a Firewall with UFW on an Ubuntu and Debian Cloud Server][ufw_DO]

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

view the current configuration of UFW

```
$ ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
8883                       ALLOW IN    Anywhere
443                        ALLOW IN    Anywhere
80                         ALLOW IN    Anywhere
8083                       ALLOW IN    Anywhere
8883 (v6)                  ALLOW IN    Anywhere (v6)
443 (v6)                   ALLOW IN    Anywhere (v6)
80 (v6)                    ALLOW IN    Anywhere (v6)
8083 (v6)                  ALLOW IN    Anywhere (v6)
```

delete the rules can be done

```
$ sudo ufw delete allow 80/tcp
OR
$ sudo ufw status numbered
$ sudo ufw delete 1
```

### Step 5 - SSL

[ssl_DO]: https://www.digitalocean.com/community/tutorials/how-to-use-certbot-standalone-mode-to-retrieve-let-s-encrypt-ssl-certificates-on-ubuntu-20-04
[besn_tls]: https://medium.com/@besnikbelegu/enabling-tls-for-mosquitto-using-lets-encrypt-and-certbot-bf10bc863db

- [How To Use Certbot Standalone Mode to Retrieve Let's Encrypt SSL Certificates on Ubuntu 20.04][ssl_DO]
- [Enabling Transport Layer Security (TLS) for MQTT Mosquitto using Let's Encrypt and certbot][besn_tls]

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

use the --standalone option to tell Certbot to handle the challenge using its own built-in web server. The -d flag is used to specify the domain you’re requesting a certificate for.

```
$ sudo certbot certonly --standalone -d your_domain
```

check the cert.pem, chain.pem, privkey.pem available to refer to

```
$ sudo ls /etc/letsencrypt/live/your_domain
README  cert.pem  chain.pem  fullchain.pem  privkey.pem
```

Certificates renewing automatically, but still need a way to run restart or reload the server to pick up the new certificates

```
$ sudo nano /etc/letsencrypt/renewal/your_domain.conf
```

_add the following line_

```
renew_hook = systemctl restart mosquitto
```

run a Certbot dry run to make sure the syntax is ok

```
$ sudo certbot renew --dry-run
```

### Step 6 - Mosquitto

[mosq_DO]: https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-ubuntu-18-04#step-4-configuring-mqtt-over-websockets-optional
[besn_setup]: https://medium.com/@besnikbelegu/setting-up-an-mqtt-server-part-1-87b7bd7d30fd

- [How to Install and Secure the Mosquitto MQTT Messaging Broker on Ubuntu 18.04][mosq_DO]
- [Setting up an MQTT Server][besn_setup]

update the package

```
$ sudo apt update
```

install Mosquitto

```
$ sudo apt install mosquitto mosquitto-clients
```

validate the installed broker

```
$ mosquitto_sub -h localhost -t test (terminal A)
$ mosquitto_pub -h localhost -t test -m "hello world" (terminal B)
```

Configure the user and password

```
$ sudo mosquitto_passwd -c /etc/mosquitto/passwd sammy
```

crerte a new configuration file for Mosquitto to use the password (end with .conf)

```
$ sudo vim /etc/mosquitto/conf.d/default.conf
```

- paste the following
- add the trailing line at the bottom

```
allow_anonymous false
password_file /etc/mosquitto/passwd
```

restart the mosquitto to take effective

```
$ sudo systemctl restart mosquitto
```

- validate the password is effective
- the tail command can show the last command status
- change the `sammy` and `password`

```
$ sudo tail -f /var/log/mosquitto/mosquitto.log (terminal A)
$ mosquitto_pub -h localhost -t "test" -m "hello world" -u "sammy" -P "password" (terminal B)
```

link the ssl certificates

```
$ sudo vim /etc/mosquitto/conf.d/default.conf
```

- add the followig lines
- change the `mqtt.example.com` to your domain
- add the trailing line at the bottom

```
. . .
listener 1883 localhost

listener 8883
certfile /etc/letsencrypt/live/mqtt.example.com/cert.pem
cafile /etc/letsencrypt/live/mqtt.example.com/chain.pem
keyfile /etc/letsencrypt/live/mqtt.example.com/privkey.pem
```

- restart the mosquitto
- open the port 8883 in firewall

```
$ sudo systemctl restart mosquitto
$ sudo ufw allow 8883
```

- validate the connection in SSL
- change the `mqtt.example.com` to your domain
- change the `sammy` and `password`

```
$ sudo tail -f /var/log/mosquitto/mosquitto.log (terminal A)
$ mosquitto_pub -h mqtt.example.com -t test -m "hello again" -p 8883 --capath /etc/ssl/certs/ -u "sammy" -P "password" (terminal B)
```

configure to use the Websockets

```
$ sudo vim /etc/mosquitto/conf.d/default.conf
```

- add the followig lines
- change the `mqtt.example.com` to your domain
- add the trailing line at the bottom

```
. . .
listener 8083
protocol websockets
certfile /etc/letsencrypt/live/mqtt.example.com/cert.pem
cafile /etc/letsencrypt/live/mqtt.example.com/chain.pem
keyfile /etc/letsencrypt/live/mqtt.example.com/privkey.pem
```

- restart the mosquitto
- open the port 8083 in firewall

```
$ sudo systemctl restart mosquitto
$ sudo ufw allow 8083
```

- validate MQTT client over websocket
- change the `mqtt.example.com` to your domain
- change the `sammy` and `password`

```
$ mosquitto_sub -h mqttwestcenter.windblue.xyz -t test  -p 8883 --capath /etc/ssl/certs/ -u "sammy" -P "password" (terminal A)
$ mosquitto_pub -h mqttwestcenter.windblue.xyz -t test -m "hello again" -p 8883 --capath /etc/ssl/certs/ -u "sammy" -P "password" (terminal B)
```

HiveMQ Client Browser
![alt text][capture]

[capture]: ../MQTT/assets/HiveMQ_Browser%20Client.jpg 'HiveMQ Client Browser'
[HiveMQ]: https://www.hivemq.com/demos/websocket-client/

[MQTT client browser][HiveMQ]
