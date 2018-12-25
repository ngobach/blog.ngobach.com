---
title: Cài đặt CMS 1.2
s: cai-dat-cms-1-2
date: 2017-03-06 23:51:35
tags: [cms]
---

Bài viết hướng dẫn cách cài đặt **CMS** *(Contest Management System)* trên **Ubuntu 16.04**.

<!--more-->

# Ubuntu 14.04 and CMS v 1.2

Download at

```bash
wget "https://github.com/cms-dev/cms/releases/download/v1.2.0/v1.2.0.tar.gz"
```

### Pre requirements

```shell
sudo apt-get install build-essential fpc postgresql postgresql-client \
     gettext python2.7 python-setuptools python-tornado python-psycopg2 \
     python-sqlalchemy python-psutil python-netifaces python-crypto \
     python-tz python-six iso-codes shared-mime-info stl-manual \
     python-beautifulsoup python-mechanize python-coverage python-mock \
     cgroup-lite python-requests python-werkzeug python-gevent patool
```

### Python requirements

```bash
sudo pip install -r REQUIREMENTS.txt
```

### Re config file at ./config/*

 * Remove printing service
 * Change default port
 * Change `secret_key`

### Installing

```bash
sudo ./setup.py build && sudo ./setup.py install
```

### Config postgresql

```bash
sudo su - postgres
createuser cmsuser -P
createdb -O cmsuser database
psql database -c 'ALTER SCHEMA public OWNER TO cmsuser'
psql database -c 'GRANT SELECT ON pg_largeobject TO cmsuser'
```

### Install Nginx & config proxy Pass

Use htpasswd to generate password file at **/etc/nginx/.htpasswd**.

```bash
sudo htpasswd -b /etc/nginx/.htpasswd admin p4ssw0rd
```

File **/etc/nginx/sites-available/default**

```nginx
location /rank/ {
		proxy_pass http://127.0.0.1:8081/;
		proxy_set_header Connection '';
		proxy_http_version 1.1;
		chunked_transfer_encoding off;
}
location /admin/ {
		auth_basic "Restricted";
		auth_basic_user_file /etc/nginx/.htpasswd;
		proxy_pass http://127.0.0.1:9000/;
}
location / {
		proxy_pass http://127.0.0.1:8080/;
}
```

## Running contest

File **start.sh**

```bash
#!/bin/sh
cmsRankingWebServer -d
cmsLogService 0 &
cmsRankingWebServer &
cmsResourceService -a 1 && fg
```