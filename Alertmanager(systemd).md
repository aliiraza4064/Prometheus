
## Install and Configure Alertmanager on Ubuntu

Login to Terminal as root with ``sudo su`` and start firing the commands below.

### Step 1 – Update System

> apt update && apt upgrade -y

### Step 2 – Download Alertmanager package

Go to official Prometheus [downloads page,](https://prometheus.io/download/) and copy the URL of Linux “tar” file.
Run the following command to download package. Paste the copied URL after wget in the below command:
> wget https://github.com/prometheus/alertmanager/releases/download/v0.26.0/alertmanager-0.26.0.linux-amd64.tar.gz

### Step 4 – Configure Alertmanager

Create a Prometheus user, required directories, and make prometheus user as the owner of those directories.

```
groupadd -f alertmanager
useradd -g alertmanager --no-create-home --shell /bin/false alertmanager
mkdir -p /etc/alertmanager/templates
mkdir /var/lib/alertmanager
chown alertmanager:alertmanager /etc/alertmanager
chown alertmanager:alertmanager /var/lib/alertmanager

```
### Step 5 - Unpack Prometheus AlertManager Binary

Untar and move the downloaded Prometheus AlertManager binary.

```
tar -xvf alertmanager-0.26.0.linux-amd64.tar.gz
mv alertmanager-0.26.0.linux-amd64 alertmanager-files

```

### Step 6 - Install Prometheus AlertManager

Copy alertmanager and amtool binary from prometheus-files folder to /usr/bin and change the ownership to prometheus user.

```
cp alertmanager-files/alertmanager /usr/bin/
cp alertmanager-files/amtool /usr/bin/
chown alertmanager:alertmanager /usr/bin/alertmanager
chown alertmanager:alertmanager /usr/bin/amtool

```

### Step 7 - Install Prometheus AlertManager Configuration File

Move the alertmanager.yml file from alertmanager-files to the /etc/alertmanager folder and change the ownership to alertmanager user.

```
cp alertmanager-files/alertmanager.yml /etc/alertmanager/alertmanager.yml
chown alertmanager:alertmanager /etc/alertmanager/alertmanager.yml

```

### Step 8 - Setup Prometheus AlertManager Service

Create an alertmanager service file.
> vi /usr/lib/systemd/system/alertmanager.service

Add the following configuration and save the file.
```
[Unit]
Description=AlertManager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/bin/alertmanager \
    --config.file /etc/alertmanager/alertmanager.yml \
    --storage.path /var/lib/alertmanager/

[Install]
WantedBy=multi-user.target

```
> chmod 664 /usr/lib/systemd/system/alertmanager.service

### Step 9 - Reload systemd and Register Prometheus AlertManager

Reload the systemd service to register and Start the Prometheus AlertManager service.

```
sudo systemctl daemon-reload
sudo systemctl start alertmanager

```
Check the alertmanager service status using the following command.
> systemctl status alertmanager

Configure Prometheus AlertManager to start at boot.
> systemctl enable alertmanager.service
If firewalld is enabled and running, add a rule for port 9093.
```
sudo firewall-cmd --permanent --zone=public --add-port=9093/tcp
sudo firewall-cmd --reload

```
### Step 9 - Access Prometheus AlertManager UI

Now you will be able to access the AlertManager UI on 9093 port of the alertmanager server.

> http://<alertmanager-ip>:9093