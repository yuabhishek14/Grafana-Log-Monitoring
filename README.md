# Log Monitoring with Grafana-Loki-Promtail

**Overview :** Grafana provides the platform for creating and visualizing dashboards, Loki serves as the log aggregation and storage backend with a unique label-based approach, and Promtail is the lightweight agent responsible for collecting and forwarding log data to Loki. Together, these tools form a powerful ecosystem for monitoring and analyzing log data in a flexible and scalable manner.

<p align="center">
  <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/1b91b724-36ef-4a6d-94e6-c44326ef1d06" alt="image" width="450" height="400" />
</p>


## Install Grafana on Ubuntu


#### 1. Install apt-transport-https:
This package allows APT (Advanced Package Tool) to use HTTPS for secure communication when downloading packages from repositories.

```bash
sudo apt-get install -y apt-transport-https

```

#### 2. Install software-properties-common and wget:
These are two essential packages that are often used to manage software repositories and download files over the internet.

```bash
sudo apt-get install -y software-properties-common wget
```
#### 3. Download Grafana Repository Key:
The following command downloads the Grafana repository GPG key and saves it to the specified location.

```bash
sudo wget -q -O /usr/share/keyrings/grafana.key https://packages.grafana.com/gpg.key

```
- `wget`: A command-line tool for downloading files from the web.
- `-q`: Quiet mode, which suppresses unnecessary output.
- `-O /usr/share/keyrings/grafana.key`: Specifies the output file and its location.
- `https://packages.grafana.com/gpg.key`: URL of the Grafana GPG key.

Once these steps are completed, you can proceed to add the Grafana repository to your system's APT sources list and install Grafana using the following commands:

```bash
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://packages.grafana.com/oss/deb stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana

```

- The `echo` command adds the Grafana repository to the APT sources list.
- The `tee` command writes the repository information to the specified file.
- `sudo apt-get update` updates the package list with the newly added repository.
- `sudo apt-get install grafana` installs the Grafana package.

#### Start Grafana 
1.  ```bash
    sudo systemctl start grafana-server
    ```

2. Enable grafana to start automatically even if system restarts :

    ```bash
    sudo systemctl enable grafana-server
    ```
3.  Since Grafana works on 3000 port so open your http://Host_IP:3000

   you will get the Grafana login screen

   <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/9f72ffb8-0bc2-4453-ae9b-2b7a17a1f930" alt="image" width="450" height="380" />
  
   login with username and password both as admin

## Install Loki and Promtail using Docker

#### Install Docker

```bash
sudo apt-get install docker.io
```

#### Add the current user to the "docker" group 

```bash
sudo usermod -aG docker $USER
```

Create a directory where we will save the Loki and Promtail configs

```bash
mkdir grafana_configs
```

Go inside the directory and download both config of Loki and Promtail

**Download Loki Config**
```bash
wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml
```

Config:
```bash
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  instance_addr: 127.0.0.1
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

query_range:
  results_cache:
    cache:
      embedded_cache:
        enabled: true
        max_size_mb: 100

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

ruler:
  alertmanager_url: http://localhost:9093

# By default, Loki will send anonymous, but uniquely-identifiable usage and configuration
# analytics to Grafana Labs. These statistics are sent to https://stats.grafana.org/
#
# Statistics help us better understand how Loki is used, and they show us performance
# levels for most users. This helps us prioritize features and documentation.
# For more information on what's sent, look at
# https://github.com/grafana/loki/blob/main/pkg/usagestats/stats.go
# Refer to the buildReport method to see what goes into a report.
#
# If you would like to disable reporting, uncomment the following lines:
#analytics:
#  reporting_enabled: false

```

**Download Promtail Config**
```bash
wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml
```

Config :
```bash
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log
```

Run Loki Docker container

```bash
docker run -d --name loki -v $(pwd):/mnt/config -p 3100:3100 grafana/loki:2.8.0 --config.file=/mnt/config/loki-config.yaml
```

Run Promtail Docker container

```bash
docker run -d --name promtail -v $(pwd):/mnt/config -v /var/log:/var/log --link loki grafana/promtail:2.8.0 --config.file=/mnt/config/promtail-config.yaml
```

once containers are up try to access the Loki service by going to http://Host_IP:3100/ready to check if Loki is correctly configured .

Once its ready we can see logs on http://Host_IP:3100/metrics
 #### Setup DataSource on Grafana 
 Go to Add Datasource and select Loki , then add the URL of Loki and click on add .

 <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/4456fa3e-e09a-498c-b589-938eca72e2fc" alt="image" width="470" height="340" />

#### Prepare Dashboard 

- Now if we go to explore we will get this :

 <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/04e3af2d-71be-4ec4-a2e7-a739caac876e" alt="image" width="700" height="340" />

Here if we select **label** as jobs and value as **varlogs** we will get all the logs which are getting generated in the system . It is a central location where various logs are generated and stored. These logs encompass a wide range of system activities, events, and services that occur on the machine. Different applications, processes, and system components contribute to generating these logs, providing valuable information for system administrators, developers, and security personnel to monitor, troubleshoot, and maintain the system effectively. 

- Now if we want only particular logs like if we want to see logs of Grafana then we can add another target in loki-config.yaml called grafan_logs like this
  ```bash
  - targets:
      - localhost
    labels:
      job: grafana_logs
      __path__: /var/log/grafana/*log
  ```

updated loki-config.yaml :

   ```bash
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log
  - targets:
      - localhost
    labels:
      job: grafana_logs
      __path__: /var/log/grafana/*log

   ```

Now if we try to make a Dashboard to see ho many times "error" word came in the last 3 hours in grafana logs we can do it like this :

<img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/5cb0d7b3-7b57-498a-a980-b0bfd646b2d7" alt="image" width="700" height="340" />

