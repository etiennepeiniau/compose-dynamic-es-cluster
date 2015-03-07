# Dynamic ElasticSearch Cluster 

This demo shows how to create an ElasticSearch cluster with a dynamic number of nodes using Docker Compose.

First, you need to install [Docker and Docker Compose](https://docs.docker.com/compose/#installation-and-set-up).

## Create the cluster
To create the cluster after cloning this repository, you need to start Docker Compose (detached mode):
```
docker-compose up -d
```
This command creates one docker containers with an ElasticSearch instance inside and one data volume container. A data volume container sole purpose is to share its volume(s) with others containers (see [Data Volume Container](https://docs.docker.com/userguide/dockervolumes/#creating-and-mounting-a-data-volume-container)). The data  volume container is going to be used by all the ElasticSearch instances that we are going to create dynamically.

You can check the container status using the command:
```
docker-compose ps
```

To interact with ElasticSearch , we need to find the binded port on our machine:
```
port=$(docker-compose port es 9200 | cut -d':' -f 2)
echo $port
```
Note the port number and replace the placeholder **$(port)** inside the next commands of this demo with it.

## Install head plugin
The head plugin is an ElasticSearch plugin supported by the community. It provides a simple and visual way to check the health of a cluster. You can find more explainations on its [GitHub page](https://github.com/mobz/elasticsearch-head) 

To install the plugin, you need to execute the following command:
```
docker-compose run --rm es /elasticsearch/bin/plugin -i mobz/elasticsearch-head
```

You can now check the cluster health at: [http://localhost:$(port)/_plugin/head/](http://localhost:$(port)/_plugin/head/)

## Loading some data
You can load the sample dataset inside the cluster using the Bulk API:
```
curl -XPOST 'localhost:$(port)/bank/account/_bulk?pretty' --data-binary @dataset/accounts.json
```
The sample dataset used has been found on [ElasticSearch site](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/_exploring_your_data.html)

## Scaling the cluster
You can now scale up and down the cluster to see how it reacts:
```
docker-compose scale es=2
docker-compose scale es=10
docker-compose scale es=4
```

