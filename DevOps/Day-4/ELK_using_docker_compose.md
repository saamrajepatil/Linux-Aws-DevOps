## **Folder Structure**

```
elk-stack/
├── docker-compose.yml
├── filebeat.yml
└── metricbeat.yml
```

---

## **1. docker-compose.yml**

```yaml
version: "3.8"

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.17.12
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - ES_JAVA_OPTS=-Xms1g -Xmx1g
    ports:
      - "9200:9200"
    networks:
      - elk
    volumes:
      - es_data:/usr/share/elasticsearch/data

  kibana:
    image: docker.elastic.co/kibana/kibana:7.17.12
    container_name: kibana
    ports:
      - "5601:5601"
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
    networks:
      - elk

  filebeat:
    image: docker.elastic.co/beats/filebeat:7.17.12
    container_name: filebeat
    user: root
    volumes:
      - ./filebeat.yml:/usr/share/filebeat/filebeat.yml
      - /var/lib/docker/containers:/var/lib/docker/containers:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - elk
    depends_on:
      - elasticsearch
      - kibana

  metricbeat:
    image: docker.elastic.co/beats/metricbeat:7.17.12
    container_name: metricbeat
    user: root
    volumes:
      - ./metricbeat.yml:/usr/share/metricbeat/metricbeat.yml
      - /var/run/docker.sock:/var/run/docker.sock:ro
    networks:
      - elk
    depends_on:
      - elasticsearch
      - kibana

volumes:
  es_data:

networks:
  elk:
```

---

## **2. filebeat.yml**

```yaml
filebeat.inputs:
  - type: container
    paths:
      - '/var/lib/docker/containers/*/*.log'
    json.add_error_key: true
    json.message_key: log

output.elasticsearch:
  hosts: ["elasticsearch:9200"]

setup.kibana:
  host: "kibana:5601"

setup.dashboards.enabled: true
```

---

## **3. metricbeat.yml**

```yaml
metricbeat.modules:
  - module: docker
    metricsets:
      - container
      - cpu
      - diskio
      - event
      - healthcheck
      - info
      - memory
      - network
    hosts: ["unix:///var/run/docker.sock"]
    period: 10s
    enabled: true

output.elasticsearch:
  hosts: ["elasticsearch:9200"]

setup.kibana:
  host: "kibana:5601"

setup.dashboards.enabled: true
```

---

## **4. Start the Stack**

```bash
cd elk-stack
docker-compose up -d
```

Check containers:

```bash
docker ps
```

---

## **5. Access Kibana**

* Open **[http://localhost:5601](http://localhost:5601)**
* Go to **Dashboard → \[Metricbeat System] Host overview ECS** for metrics
* Go to **Dashboard → \[Filebeat] Overview ECS** for logs

---

## **6. How Metrics Flow**

1. **Filebeat** → Collects container logs → Sends to Elasticsearch → Visualized in Kibana.
2. **Metricbeat** → Collects system & Docker metrics → Sends to Elasticsearch → Visualized in Kibana dashboards.
3. Beats use built-in dashboard templates when `setup.dashboards.enabled: true`.


Do you want me to add that?
