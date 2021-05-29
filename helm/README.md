# Operations

## Table of Contents
* [UFW](#UFW)
* [Nginx](#Nginx)
* [Systemd](#Systemd)
* [Release](#Release)

## UFW

Run

```shell
ufw allow ssh
ufw allow http
ufw allow https
ufw enable
```

## Nginx

* Create a Nginx config file in /etc/nginx/sites-available/template.example.com:

```nginx
upstream template {
    server 127.0.0.1:8080; 
}

server {
    listen 80;
    server_name template.example.com;

    location / {
        proxy_pass  http://template;
    }
}
```  

* Enable, configure and start Nginx

```shell
cd /etc/nginx/sites-enabled
ln -s ../sites-available/template.example.com .
nginx -t
certbot 
```

## Systemd

Create `/etc/systemd/system/template.service` with:

```unit file (systemd)
[Unit]
Description=Template container starter
After=docker.service network-online.target
Requires=docker.service network-online.target

[Service]
WorkingDirectory=/opt/template
Type=oneshot
RemainAfterExit=yes

ExecStartPre=-/usr/local/bin/docker-compose pull --quiet
ExecStart=/usr/local/bin/docker-compose -f docker-compose.yml up -d

ExecStop=/usr/local/bin/docker-compose -f docker-compose.yml down

ExecReload=/usr/local/bin/docker-compose pull --quiet
ExecReload=/usr/local/bin/docker-compose -f docker-compose.yml up -d

[Install]
WantedBy=multi-user.target
```

and run:
```shell
systemctl daemon-reload
systemctl enable template
service template start
```

## Release

Run:
```shell
cd /opt/template
git pull
service template restart
```
