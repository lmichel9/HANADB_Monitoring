# HANADB_Monitoring
Installation of hanadb_exporter, grafana and prometheus from a Dockerfile on openSUSE leap 15.4 (suitable on SLES15)

This documentation is made to facilitate the installation in 4 steps of the exporter with docker and docker swarm

There is also an official documentation (by openSUSE) to install hanadb_exporter avalaible at https://github.com/SUSE/hanadb_exporter

# REQUIREMENTS:
-> Docker on your system 

-> Docker swarm (docker swarm init)


# 1.Get docker images 
Firstly you can get images with docker pull : 

docker pull louisap/opensuse_grafana_prometheus #for grafana and prometheus

docker pull louisap/hanadb_exporter #for hanadb_exporter 

If you want to modify or view instructions you can visit https://github.com/lmichel9/Docker to get them in .txt


# 2.Get config files 
Secondly you have to download config.json and prometheus.yml 

git clone https://github.com/lmichel9/HANADB_Monitoring.git

You don't need to modify prometheus.yml !

You have to COMPLETE config.json with your informations :


1-> ip adress or hostname of instance where exporter will be installed


2-> instance number to complete the port : 

instance number => 02 

port => 30213 (30013 is default port for SYSTEMDB tenant)


3-> you need to create users for each tenant you want to monitor (1 or more) :

They must have the same name and the same password !

TODO in each tenant: 

CREATE USER HANADB_EXPORTER_USER PASSWORD MyExporterPassword NO FORCE_FIRST_PASSWORD_CHANGE;

CREATE ROLE HANADB_EXPORTER_ROLE;

GRANT MONITORING TO HANADB_EXPORTER_ROLE;

GRANT HANADB_EXPORTER_ROLE TO HANADB_EXPORTER_USER;  


Be careful with your password the JSON config file is vulnerable to special characters !


# 3.Create config, network and volume 

create config for services you must have config file prometheus.yml and config.json :

docker config create config_prometheus ./prometheus.yml

docker config create config_hanadb ./config.json 

create network for services :

docker network create -d overlay --attachable net_graf_prom

create a volume for grafana :

docker volume create grafana-storage


# 4.install and test services

install hanadb_exporter :

docker service create --name hanadb -p 9668:9668 --network net_graf_prom --hostname hanadb.com --config source=config_hanadb,target=/usr/etc/hanadb_exporter/config.json louisap/hanadb_exporter

test it at <ip_adress>:9668 it should return metrics

install grafana and promehteus :

docker service create --name grafa_prom -p 9090:9090 -p 3000:3000 --network net_graf_prom --config source=config_prometheus,target=/home/prometheus/prometheus-2.45.0-rc.0.linux-amd64/prometheus.yml --mount src=grafana-storage,dst=/home/grafana/ louisap/opensuse_grafana_prometheus

test prometheus at <ip_adress>:9090

test grafana at <ip_adress>:3000


# NB:

Modify config file (prometheus.yml or config.json) :

docker service update --config-rm <old_config> --config-add source=<new_config>,target=<path_to_file_with_file> <service_name>

example : docker service update --config-rm config_prometheus --config-add source=config_prometheus.v2,target=/home/prometheus/prometheus-2.45.0-rc.0.linux-amd64/prometheus.yml grafa-prom

Start and stop services :

docker service scale <service_name>=0 #stop service

docker service scale <service_name>=1 #start service

















