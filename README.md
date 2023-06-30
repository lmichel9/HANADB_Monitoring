# HANADB_Monitoring
Installation of hanadb_exporter, grafana and prometheus from a Dockerfile on openSUSE leap 15.4 (suitable on SLES15)

# -REQUIREMENTS:
->Docker on your system 
->Docker swarm (docker swarm init)

1.Get docker images 
Firstly you can get images with docker pull : 

docker pull louisap/opensuse_grafana_prometheus #for grafana and prometheus

docker pull louisap/hanadb_exporter #for hanadb_exporter

If you want to modify Dockerfiles you can visit https://github.com/lmichel9/Docker to get them in .txt




