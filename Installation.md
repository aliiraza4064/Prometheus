
## Install and Configure Prometheus on Ubuntu

Login to Terminal as root with ``sudo su`` and start firing the commands below.

### Step 1 – Update System

> apt update
> apt upgrade -y

### Step 2 – Disable SELinux

Open SELinux configuration and edit the file:

> sudo vi /etc/sysconfig/selinux

Change “SELINUX=enforcing” to “SELINUX=disabled”.

Save and exit the file. Then reboot the system.

> sudo reboot

### Step 3 – Download Prometheus package

Go to official Prometheus [downloads page,](https://prometheus.io/download/) and copy the URL of Linux “tar” file.
Run the following command to download package. Paste the copied URL after wget in the below command:
>wget https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz

### Step 4 – Configure Prometheus

Add a Prometheus user.

> useradd --no-create-home --shell /bin/false prometheus

Create needed directories.

> mkdir /etc/prometheus

> mkdir /var/lib/prometheus

Change the owner of the above directories.

> chown prometheus:prometheus /etc/prometheus

> chown prometheus:prometheus /var/lib/prometheus

Now go to Prometheus downloaded location and extract it.

> tar -xvzf prometheus-2.48.0.linux-amd64.tar.gz

Rename it as per your preference.

> mv prometheus-2.48.0.linux-amd64 prometheuspackage

Copy “prometheus” and “promtool” binary from the “prometheuspackage” folder to “/usr/local/bin”.

> cp prometheuspackage/prometheus /usr/local/bin/

> cp prometheuspackage/promtool /usr/local/bin/

Change the ownership to Prometheus user.

> chown prometheus:prometheus /usr/local/bin/prometheus

> chown prometheus:prometheus /usr/local/bin/promtool

Copy “consoles” and “console_libraries” directories from the “prometheuspackage” to “/etc/prometheus folder”

> cp -r prometheuspackage/consoles /etc/prometheus

> cp -r prometheuspackage/console_libraries /etc/prometheus

Change the ownership to Prometheus user

> chown -R prometheus:prometheus /etc/prometheus/consoles

> chown -R prometheus:prometheus /etc/prometheus/console_libraries

Add and modify Prometheus configuration file.

Configurations should be added to the “/etc/prometheus/prometheus.yml”

Now we will create the prometheus.yml file.

> vim /etc/prometheus/prometheus.yml

Add the following configuration to the file.
```
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus_master'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
```

save and exit the file

Change the ownership of the file.

> chown prometheus:prometheus /etc/prometheus/prometheus.yml

Configure the Prometheus Service File.

> vim /etc/systemd/system/prometheus.service

Copy the following content to the file.
```
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

Save and the exit file.

Reload the systemd service.

> systemctl daemon-reload

Start the Prometheus service.

> systemctl start prometheus

Check service status.

> systemctl status prometheus

Reload firewall service.

> systemctl reload firewalld

### Step 5 – Access Prometheus Web Interface

Use the following Url to access UI.

http://Server-IP:9090/graph
