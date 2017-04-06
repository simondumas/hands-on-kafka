
## Run landoop/fast-data-dev on linux

docker run --rm --net=host -v ~/projects/devoxx-handson-java:/data landoop/fast-data-dev

## Run landoop/fast-data-dev on mac


On Mac OS X allocate at least 6GB RAM to the VM:

    docker-machine create --driver virtualbox --virtualbox-cpu-count "4"  --virtualbox-memory "6024" devel
    eval $(docker-machine env devel)


Excecute the result of

```
docker-machine env devel
```

And define ports and advertise hostname:
 <!>  Replace  `myPathToTheProject` with a real path <!>

```
docker run --rm -p 2181:2181 -p 3030:3030 -p 8081-8083:8081-8083 \
           -p 9581-9585:9581-9585 -p 9092:9092 -v /myPathToTheProject/hands-on-kafka/data:/data -e ADV_HOST=192.168.99.100  -e CONNECT_HEAP=3G -e FORWARDLOGS=0 -e RUNTESTS=0 -e DEBUG=1 \
           landoop/fast-data-dev:latest

```

## Configuation Kafka Connect

For ingestion of commits.json

```
{
  "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
  "file": "/data/commits.json",
  "tasks.max": "1",
  "name": "CommitsConnector",
  "topic": "commits"
}
```

For ingestion of contributors.json

```
{
  "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
  "file": "/data/contributors.json",
  "tasks.max": "1",
  "name": "ContributorsConnector",
  "topic": "contributors"
}
```


## How to scrap Github API ?
for i in {1..11}; do ( http -a user:pass --pretty=none https://api.github.com/repos/scala/scala/contributors\?page\=$i --pretty=none | jq --compact-output '.[]' ) >> /tmp/contributors.json done
for i in {1..901}; do ( http -a user:pass --pretty=none https://api.github.com/repos/scala/scala/commits\?page\=$i --pretty=none | jq --compact-output '.[]' ) >> ./commits.json done