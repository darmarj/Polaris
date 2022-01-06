---
template: overrides/main.html
description: >
  Proxmox v7 Subscription disable
search:
  exclude: true
---

# Bitwarden_rs

__This is a Bitwarden server API implementation written in Rust compatible with upstream Bitwarden clients*, perfect for self-hosted deployment where running the [official](https://github.com/liuchenx/bitwarden_rs) resource-heavy service might not be ideal.__

## Features
Basically full implementation of Bitwarden API is provided including:

 * Basic single user functionality
 * Organizations support
 * Attachments
 * Vault API support
 * Serving the static files for Vault interface
 * Website icons API
 * Authenticator and U2F support
 * YubiKey OTP

## Installation
Cert:
``` bash
openssl req -x509 -newkey rsa:4096 -keyout $NAME.key \
-out $NAME.crt -days 720 -nodes
```
Container:
``` bash
e.g. 
$PATH = /docker_data/bitwarden/ssl
$NAME = bitwarden

docker run -d --restart always --name $NAME -e \
ROCKET_TLS='{certs="/$PATH/$NAME.crt",key="/$PATH/$NAME.key"}' \
-v /$PATH/ssl/:/ssl/ -v \
/$PATH/:/data/ -p 443:80 bitwardenrs/server:latest
```
DB Backup:
``` bash
#!/usr/bin/env bash

set -e
set -u

declare -r db_src="$PATH/db.sqlite3"
declare -r db_bak="$PATH/backup"
declare -r db_dst="$SMB/NFS/AW3/DROPBOX..."
 
rm -rf "${db_bak}/*"
sqlite3 "${db_src}" ".backup '${db_bak}/bitw.sqlite3'"

cd "${db_bak}"
tar -czf "bitw.tar.gz" "bitw.sqlite3"
mv "${db_bak}/bitw.tar.gz" "${db_dst}/bitw.tar.gz-$(date +"%Y-%m-%d")"

crontab -e
0 3 * * * $PATH/*.sh	# Rsync on 3:00 AM

crontab -l				# List the schedule
```
!!! Tip
	[sqlite3](https://sqlite.org/index.html): curl the source and complie.
	``` bash
	# dnf install make, gcc
	# tar xvfz sqlite-autoconf-<version>.tar.gz
	# cd sqlite-autoconf-<version>
	# ./configure
	# make
	# make install
	```
