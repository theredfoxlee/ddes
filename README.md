# DDES
*Drag and Drop Elastic Stack* is an infrastructure prepared to visualize data published by user via web interface.

## Workflow
1. **User** is publishing files via web interface (**droppy**).
2. Received files are being transferred automatically (via **filebeat**) to analyser (**logstash**).
3. Parsed data are being automatically stored in database (**elasticsearch**).
4. **User** accesses the data via visualziation plugin (**kibana**).

## Deployment

### Leader (Manager)
```bash
sysctl -w vm.max_map_count=262144 # required for elasticsearch bootstraping
docker swarm init
LEADER="$(docker node ls | grep -E '^[^ ]{25} \*' | awk '{print $1}')"
docker node update --label-add Kibana=true "${LEADER}"
docker node update --label-add Droppy=true "${LEADER}"
docker stack deploy -c docker-compose.yml ddes
```

[![asciicast](https://asciinema.org/a/DVI2volRjVsZxBAAE3sNMNQix.svg)](https://asciinema.org/a/DVI2volRjVsZxBAAE3sNMNQix)

### Worker
```bash
sysctl -w vm.max_map_count=262144
docker swarm join --token <token> <host>:<port> # based on docker swarm init output
```

## Endpoints

### GUI
Kibana can be accessed via port **5601**.
Droppy can be accessed via port **8989**.

### Data management
Elasticsearch can be managed via REST API published on port **9200**.

#### Logs
Logs of each service in stack can be accessed via docker: `docker service logs <service_name>`
