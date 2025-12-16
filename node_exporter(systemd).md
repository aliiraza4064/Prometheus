# Installation of Node-exporter on systemd

First, you need to configure Prometheus node exporter on a Linux server.

Copy URL of the Node Exporter form the official [download page.](https://prometheus.io/download/)

#### Node Exporter Download and configurations

Paste the copied URL after wget in the following command:

wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz

Extract the downloaded package.

> tar -xvzf node_exporter-1.10.2.linux-amd64.tar.gz

Create a user for the node exporter.

> useradd -rs /bin/false nodeusr

Move binary to “/usr/local/bin” from the downloaded extracted package.

> mv node_exporter-1.10.2.linux-amd64/node_exporter /usr/local/bin/

Create a service file for the node exporter.

> vi /etc/systemd/system/node_exporter.service

Add the following content to the file.
```
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=nodeusr
Group=nodeusr
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
Save and exit the file.

Reload the system daemon.

> systemctl daemon-reload

Start node exporter service.

> systemctl start node_exporter

Reload firewall service.

> systemctl restart firewalld

Enable node exporter on system boot.

> systemctl enable node_exporter

View the metrics browsing node exporter URL.

http://IP-Address:9100/metrics



**Node Exporter Metrics**

Add configured node exporter Target On Prometheus Server.

Login to Prometheus server and modify the prometheus.yml file

Edit the file:

> vi /etc/prometheus/prometheus.yml

Add the following configurations under the scrape config.
```
 - job_name: 'node_exporter_centos'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.94.10.209:9100']
```
Modified File

Restart Prometheus service.

> systemctl restart prometheus
