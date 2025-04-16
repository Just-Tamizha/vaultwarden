
# BitWarden (VaultWarden)

Installation steps for VaultWarden **without Docker Method.**

## Requirements

- Ubuntu

## Deployments URLs

Homepage: `localhost:8000` or `127.0.0.1:8000`

Adminpage: `/admin`

## Installation & Execution Commands

All of these commands should be performed as `root`, or prefixed with `sudo`

First, create a directory to store the Docker image temporarily.

```
mkdir vw-image
```
Move to the directory
```
cd vw-image
```
Download Docker images
```
wget https://raw.githubusercontent.com/jjlin/docker-image-extract/main/docker-image-extract
```
Permission update
```
chmod +x docker-image-extract
```
Extract the Vaultwarden Docker image
```
./docker-image-extract vaultwarden/server:alpine
```
Create directories where Vaultwarden will be stored on the server
```
mkdir /opt/vaultwarden
```
```
mkdir /var/lib/vaultwarden
```
```
mkdir /var/lib/vaultwarden/data
```
Create a `vaultwarden` user and make the Vaultwarden data owner
```
useradd vaultwarden
```
Permission update
```
chown -R vaultwarden:vaultwarden /var/lib/vaultwarden
```
Move the Vaultwarden server program and data
```
mv output/vaultwarden /opt/vaultwarden
```
```
mv output/web-vault /var/lib/vaultwarden
```
Remove the unnecessary files
```
rm -Rf output
```
```
rm -Rf docker-image-extract
```
Create the hash for a `Vaultwarden admin password`. You will be prompted for a password twice. Save the resulting output.
```
/opt/vaultwarden/vaultwarden hash
```
###
Create the file `.env`
```
nano /var/lib/vaultwarden/.env
```
Update the `.env` file
```
DOMAIN=https://www.example.com/vaultwarden/
ORG_CREATION_USERS=user@example.com
ADMIN_TOKEN='<hash produced by vaultwarden hash earlier>'
SIGNUPS_ALLOWED=false
SMTP_HOST=smtp.example.com
SMTP_FROM=vaultwarden@example.com
SMTP_FROM_NAME=Vaultwarden
SMTP_PORT=587          # Ports 587 (submission) and 25 (smtp) are standard without encryption and with encryption via STARTTLS (Explicit TLS). Port 465 is outdated and us>
SMTP_SSL=true          # (Explicit) - This variable by default configures Explicit STARTTLS, it will upgrade an insecure connection to a secure one. Unless SMTP_EXPLICIT_>
SMTP_EXPLICIT_TLS=false # (Implicit) - N.B. This variable configures Implicit TLS. It's currently mislabelled (see bug #851) - SMTP_SSL Needs to be set to true for this o>
SMTP_USERNAME=user@example.com
SMTP_PASSWORD=mysmtppassword
SMTP_TIMEOUT=15
# Change the following back to true to allow login on the web.
WEB_VAULT_ENABLED=false
LOG_FILE=/var/lib/vaultwarden/vaultwarden.log

```
###
Create the file `vaultwarden.service`
```
nano /etc/systemd/system/vaultwarden.service
```
Update the `vaultwarden.service` file
```
[Unit]
Description=Bitwarden Server (Rust Edition)
Documentation=https://github.com/dani-garcia/vaultwarden
After=network.target

[Service]
User=vaultwarden
Group=vaultwarden
EnvironmentFile=/var/lib/vaultwarden/.env
ExecStart=/opt/vaultwarden/vaultwarden
LimitNOFILE=1048576
LimitNPROC=64
PrivateTmp=true
PrivateDevices=true
ProtectHome=true
ProtectSystem=strict
WorkingDirectory=/var/lib/vaultwarden
ReadWriteDirectories=/var/lib/vaultwarden
AmbientCapabilities=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target
```

# Start - `Vaultwarden service`

Start the Vaultwarden service and check its status

To enable (`OneTimeOnly`)
```
systemctl enable vaultwarden
```
To Start
```
systemctl start vaultwarden
```
To check the status
```
systemctl status vaultwarden
```
If anything modified in `.env` or `vaultwarden.service` Restart the `Apache server`
```
systemctl restart apache2
```

## Authors

- [@Just-Tamizha](https://www.github.com/Just-Tamizha)

## Contribution

Contributions are always welcome!

Please raise the `Pull requests`
