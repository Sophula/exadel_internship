# Task 7: Logging & Monitoring

## Tasks:

## 1. Zabbix:
Big brother is watching  ....
- Install on server, configure web and base
- Prepare VM or instances. 
- Install Zabbix agents on previously prepared servers or VM.

EXTRA 1.2.2: Complete 1.2.1 using ansible

- Make several of your own dashboards, where to output data from your host/vm/container (one of them)
- Active check vs passive check - use both types.
- Make an agentless check of any resource (ICMP ping)
- Provoke an alert - and create a Maintenance instruction
- Set up a dashboard with infrastructure nodes and monitoring of hosts and software installed on them

Installed Docker Engine and Docker Compose on vm, opened 80/443/10050/10051 ports.

Docker Engine:

```
sudo apt install openssh-server
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
docker version
```

Docker Compose:

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
Install Zabbix Repo:
```
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu20.04_all.deb
dpkg -i zabbix-release_6.0-1+ubuntu20.04_all.deb
apt update
```
Install Zabbix server, frontend, agent: 
```
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```
[Create Initial Database, Configure the database for Zabbix server, Start Zabbix server and agent processes, Configure Zabbix frontend](https://www.zabbix.com/download?zabbix=6.0&os_distribution=ubuntu&os_version=20.04_focal&db=mysql&ws=apache)

Install Zabbix Agent:
```
sudo wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-1+ubuntu20.04_all.deb
sudo dpkg -i zabbix-release_6.0-1+ubuntu20.04_all.deb
sudo apt update
sudo apt install zabbix-agent
sudo systemctl status zabbix-agent
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
sudo systemctl status zabbix-agent
sudo vim /etc/zabbix/zabbix_agentd.conf
sudo systemctl restart zabbix-agent
sudo ufw enable
sudo ufw allow 10050/tcp
sudo ufw status
```


## 2. ELK:
Nobody is forgotten and nothing is forgotten.
- Install and configure ELK
Have used docker-compose file to deploy ELK stack: Elasticsearch, Logstash and Kibana. As a volume mounted the logstash.conf inside the container.
Command to run the docker-compose file:
```
docker-compose up -d
```

- Organize collection of logs from docker to ELK and receive data from running containers

Have started additional nginx container to organize collection of logs from docker to ELK. 

```
docker run -d -p 8080:80 --log-driver gelf --log-opt gelf-address=udp://localhost:12201 nginx:latest
```

Used ‘Discover’ section and on created Index Pattern, using ‘*’ and ‘@timestamp’.

![image](https://user-images.githubusercontent.com/85607071/159227133-a9a156bf-cc95-40a5-9067-f1595c7e4aee.png)

- Customize your dashboards in ELK
Have installed Docker metrics:
```
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.1.1-amd64.deb
sudo dpkg -i metricbeat-7.1.1-amd64.deb
```

Modified /etc/metricbeat/metricbeat.yml
```
output.elasticsearch:
  hosts: ["<es_url>"]
  username: "elastic"
  password: "<password>"
setup.kibana:
  host: "<kibana_url>"
```

```
sudo metricbeat modules enable docker
sudo metricbeat setup
sudo service metricbeat start
```

EXTRA 2.4: Set up filters on the Logstash side (get separate docker_container and docker_image fields from the message field)

- Configure monitoring in ELK, get metrics from your running containers
- [Study features and settings](https://www.elastic.co/elastic-stack/features)

## 3. Grafana:
- Install Grafana
```
sudo apt-get install -y apt-transport-https
sudo apt-get install -y software-properties-common wget
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add 
echo "deb https://packages.grafana.com/enterprise/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get install grafana-enterprise
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl status grafana-server
sudo systemctl enable grafana-server
```
- Integrate with installed ELK

![image](https://user-images.githubusercontent.com/85607071/159247123-4fcfc0e2-6422-44f4-b31a-bb1d26913c59.png)

- Set up Dashboards

![image](https://user-images.githubusercontent.com/85607071/159233152-71c912f2-4684-46e8-819d-52e248b4d443.png)

- [Study features and settings](https://www.plesk.com/blog/various/grafana-monitoring-solution/)
