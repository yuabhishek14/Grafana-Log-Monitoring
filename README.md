# Log Monitoring with Grafana-Loki-Promtail

**Overview :** Grafana provides the platform for creating and visualizing dashboards, Loki serves as the log aggregation and storage backend with a unique label-based approach, and Promtail is the lightweight agent responsible for collecting and forwarding log data to Loki. Together, these tools form a powerful ecosystem for monitoring and analyzing log data in a flexible and scalable manner.

<img src="https://github.com/yuabhishek14/Grafana-Log-Monitoring/assets/43784560/1b91b724-36ef-4a6d-94e6-c44326ef1d06" alt="image" width="450" height="400" />


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

Remember that these commands set up Grafana from the official Grafana repository, ensuring you're installing the latest version of Grafana.


