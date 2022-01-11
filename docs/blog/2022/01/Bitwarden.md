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
$SSL_PATH = bitwarden/ssl
$CONTAINER NAME = bitwarden

docker run -d --restart always --name $CONTAINER NAME -e \
ROCKET_TLS='{certs="/$SSL_PATH/$SSL_NAME.crt",key="/$SSL_PATH/$SSL_NAME.key"}' \
-v /$HOME/bitwarden/ssl/:/ssl/ \
-v /$HOME/bitwarden/bw-data:/data/ \
-p 443:80 bitwardenrs/server:latest
```
[DB Backup](https://github.com/dani-garcia/vaultwarden/issues/975):
``` bash
#!/usr/bin/env bash

set -e

bitwarden_folder_updated=/home/USER/bitwarden/bitwarden-folder-updated
touch $bitwarden_folder_updated

if [[ "$(cat $bitwarden_folder_updated)" == "1" ]]; then
  rm -f /home/USER/bw-bk.tar.gz

  docker exec bitwarden bash -c 'mkdir -p /data/db-backup && sqlite3 /data/db.sqlite3 ".backup /data/db-backup/backup.sqlite3"'

  cd /home/USER/bitwarden/bw-data
  tar -czvf /home/USER/bw-bk.tar.gz \
    config.json \
    icon_cache \
    db-backup/backup.sqlite3

  cd /home/USER/bitwarden/
  tar -czvf /home/USER/bw-scripts.tar.gz \
    backup.sh \
    bitwarden-watch-for-changes.service \
    watch-for-changes.sh

mv /home/USER/bw-bk.tar.gz /SMB,NFS.AW3.../BitwardenBak/bw-bk-$(date +"%Y-%m-%d").tar.gz

  echo "0" > /home/USER/bitwarden/bitwarden-folder-updated
else
  echo 'nothing to backup'
fi

crontab -e
0 3 * * * $PATH/*.sh > /dev/null 2>&1	# Rsync on 3:00 AM

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
!!! Tip
	sqlite3 in docker env:
	```
	# docker exec -it CONTAINER bash -c 'cp /datasource/sqlite3 /usr/local/bin'
	```
