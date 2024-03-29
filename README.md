# Visualizing Grafana dashboard using Loki and Promtail

![image](https://github.com/Pavan-1997/Grafana_Loki_Promtail/assets/32020205/0d47f55c-b104-4d03-85b4-8793f5eadce5)

Loki is log aggregation tool
 
Prometheus is a metrics monitoring tool.

Promtail, is a lightweight log collector developed by Grafana Labs and part of the Prometheus monitoring ecosystem. It serves as the agent responsible for collecting logs and forwarding them to Loki.

---

1. Install EC2 Ubuntu Instance 


2. Update the system:
```
sudo apt-get update

sudo apt-get install -y apt-transport-https

sudo apt-get install -y software-properties-common wget
```

3. Download Grafana:
```
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key

* Stable release-

echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

* Beta release-

echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com beta main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

4. Update the list of available packages:
```
sudo apt-get update
```

5. Install Grafana:
```
sudo apt-get install grafana
```
a) Start the Grafana-
```
sudo systemctl start grafana-server
```
b) Enable the at boot time-
```
sudo systemctl enable grafana-server
```
c) Open port `3000` in EC2 and access using the IP

d) Login with `admin:admin`


7. Install Docker:
```
sudo apt-get install docker.io

sudo usermod -aG docker $USER
```
```
sudo reboot
```
`Reboot the instance for Ubuntu user to execute docker commands`


8. Install Loki using Docker:

a) Download Loki Config-
```
mkdir grafana-configs

cd grafana-configs

wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml
```
b) Run Loki Docker container-
```
sudo docker run -d --name loki -v $(pwd):/mnt/config -p 3100:3100 grafana/loki:2.8.0 --config.file=/mnt/config/loki-config.yaml
```
c) Now open port 3100 in EC2 and access using the IP

`<IP>:3100/ready --- Wait for 15 sec and then goes to ready`

`<IP>:3100/metrics --- To check what are the metrics`


9. Install Promtail using Docker:

a) Download Promtail Config-
```
wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml
```
b) Run Promtail Docker container-
```
sudo docker run -d --name promtail -v $(pwd):/mnt/config -v /var/log:/var/log --link loki grafana/promtail:2.8.0 --config.file=/mnt/config/promtail-config.yaml
```

10. Now go to Grafana and click on Add Datastore - Give http://localhost:3100/metrics
	
	Select job
	
	Select varlogs
	
	Now click on run 
	

11. Filtering a particular logs

	`cd  /var/log`
	
	`ls`
	
	`cd grafana.log`
	
	`pwd`

  Copy the pwd above


12. Add target in Promtail for Grafana logs 
```
   targets: localhost
   labels:
   job: grafanalogs
   __path__: /var/log/grafana/*log
```

13. Now restart the Promtail container-
```
docker restart <container-id>
```

14. Now refresh the grafana page and now you should see grafanalogs and run it


15. Add the above to dashboard and also add Visualization


16. Install Nginx
```
sudo apt-get install nginx
```

17. Go to Grafana again in Step 15. Now select below

	Label Filters - job - varlogs
					
	Line contains - nginx
	
	Now select 5 minutes, run the query later apply
	
	Make changes accordingly 
