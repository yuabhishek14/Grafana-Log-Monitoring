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

   <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/9f72ffb8-0bc2-4453-ae9b-2b7a17a1f930" alt="image" width="450" height="400" />
  
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

Download Loki Config
```bash
wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml
```

Download Promtail Config
```bash
wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml
```

once container is up try to access the Loki service by going to http://Host_IP:3100/ready to check if Loki is correctly configured .

Once its ready we can see logs on http://Host_IP:3100/metrics
 #### Setip DataSource on Grafana 
 Go to Add Datasource and select Loki , then add the URL of Loki and click on add .

 <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/4456fa3e-e09a-498c-b589-938eca72e2fc" alt="image" width="450" height="400" />

 <img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/4456fa3e-e09a-498c-b589-938eca72e2fc" alt="image" />


