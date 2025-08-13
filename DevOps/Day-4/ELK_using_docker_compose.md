
We need to choose T2.large instance type 

refer attached doc for detailed steps:

[ELK_final.docx](https://github.com/user-attachments/files/21746789/ELK_final.docx)


✅ Part 1: Prerequisites on Amazon Linux 2
1.1 Update System and Install Docker
sudo yum update -y
sudo amazon-linux-extras enable docker
sudo yum install docker -y
sudo service docker start
sudo systemctl enable docker
sudo usermod -aG docker ec2-user  # Optional: logout & login again
1.2 Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose


sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
________________________________________
📁 Part 2: Setup ELK Using Docker Compose
2.1 Create a working directory
mkdir ~/elk-docker && cd ~/elk-docker
2.2 Create docker-compose.yml
version: '3.7'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.21
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms1g -Xmx1g"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - esdata:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:7.17.21
    container_name: logstash
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5044:5044"
    depends_on:
      - elasticsearch
    networks:
      - elk

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.21
    container_name: kibana
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    ports:
      - "5601:5601"
    depends_on:
      - elasticsearch
    networks:
      - elk

volumes:
  esdata:

networks:
  elk:
2.3 Create logstash.conf
cat <<EOF > logstash.conf
input {
  beats {
    port => 5044
  }
}

output {
  elasticsearch {
    hosts => ["http://elasticsearch:9200"]
    index => "logstash-%{+YYYY.MM.dd}"
  }
}
EOF
2.4 Start the stack
docker-compose up -d
2.5 Verify services
docker ps
curl http://localhost:9200
•	Open http://<EC2-IP>:5601 for Kibana (open port 5601 in Security Group)
________________________________________
📦 Part 3: Install Filebeat & Metricbeat on Amazon Linux 2
3.1 Import Elastic GPG Key & Repo
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
cat <<EOF | sudo tee /etc/yum.repos.d/elastic.repo
[elastic-7.x]
name=Elastic repository
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
enabled=1
autorefresh=1
type=rpm-md
EOF
3.2 Install Filebeat & Metricbeat
sudo yum install metricbeat-7.17.21 
sudo yum install filebeat-7.17.21
________________________________________
⚙️ Part 4: Configure Beats to Push Data
4.1 Configure Filebeat /etc/filebeat/filebeat.yml
Update:
output.Elasticsearch:
  hosts: ["localhost:9200"]

setup.kibana:
  host: "localhost:5601"
Enable system module:
sudo filebeat modules enable system
4.2 Configure Metricbeat /etc/metricbeat/metricbeat.yml
Update:
output. Elasticsearch:
  hosts: ["localhost:9200"]

setup.kibana:
  host: "localhost:5601"
Enable modules:
sudo metricbeat modules enable system docker
________________________________________
▶️ Part 5: Start Filebeat & Metricbeat
sudo systemctl enable filebeat --now
sudo systemctl enable metricbeat --now
________________________________________
📊 Part 6: Load Dashboards (Optional)
sudo filebeat setup
sudo metricbeat setup
Now open Kibana → Dashboard and search:
•	“Filebeat System Logs”
•	“Metricbeat Docker Metrics” or “System Overview”
________________________________________
✅ Summary
Component	Access
Kibana	http://<EC2-IP>:5601
Elasticsearch	http://<EC2-IP>:9200
Logstash	Port 5044 for Beats input
________________________________________
<img width="1599" height="821" alt="image" src="https://github.com/user-attachments/assets/96ddb794-7a93-4f40-bfd1-4d7795527d9b" />


