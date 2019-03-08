---
layout: post
title: 개발 읽을 거리 링크        
categories:
  - Tdd
tags :   
  - link    
  - FrontEnd
published: false  
---

```

docker swarm join --token SWMTKN-1-30ukp33c8stln9a995gqd1m09u9exl4n22j9bbwhj9hikmxi3s-5q0hg810wyj71puhpntf5x73j 220.123.184.113:2377


#

      
rbddopecgkcjyo2vl8ja77xq3
overall progress: 2 out of 2 tasks 
fvoomvelgqr2: running   [==================================================>] 
o5fo9ic9dgxu: running   [==================================================>] 
verify: Service converged 

#
root@gitlab-irex:/home/docker/volumes# docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE                    PORTS
rbddopecgkcj        portainer_agent     global              2/3                 portainer/agent:latest   

#
root@gitlab-irex:/home/docker/volumes# docker service rm portainer_agent
portainer_agent

#
root@gitlab-irex:/home/docker/volumes# docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
o5fo9ic9dgxu57f1yynf2exrl     aisop_2_0           Ready               Active                                  18.09.0
fvoomvelgqr2m2tigrsa7g302 *   gitlab-irex         Ready               Active              Leader              18.06.1-ce
yodtcaom5233kawhp8se324v5     ubuntu              Ready               Active                                  18.06.2-ce


#
root@gitlab-irex:/home/docker/volumes# docker service ps portainer_agent
ID                  NAME                                        IMAGE                    NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
shd4t0v907v2        portainer_agent.o5fo9ic9dgxu57f1yynf2exrl   portainer/agent:latest   aisop_2_0           Running             Running about a minute ago                       *:9003->9003/tcp
syljyet15wtj        portainer_agent.fvoomvelgqr2m2tigrsa7g302   portainer/agent:latest   gitlab-irex         Running             Running about a minute ago                       *:9003->9003/tcp
r1rx7nqlki4n        portainer_agent.yodtcaom5233kawhp8se324v5   portainer/agent:latest   ubuntu              Running             Running about a minute ago                       *:9003->9003/tcp


docker service create \
    --name portainer_agent \
    --network portainer_agent_network \
    -e AGENT_CLUSTER_ADDR=tasks.portainer_agent \
    --mode global \
    --constraint 'node.platform.os == linux' \
    --mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock \
    --mount type=bind,src=//home/docker/volumes,dst=/var/lib/docker/volumes \
    portainer/agent
      
    
docker service create \
    --name portainer \
    --network portainer_agent_network \
    --publish 9000:9000 \
    --replicas=1 \
    --constraint 'node.role == manager' \
    portainer/portainer -H "tcp://tasks.portainer_agent:9001" --tlsskipverify    
```
 
```
몽고 디비를 활용한 SWAM 모드 설정 하기
 
https://medium.com/@kalahari/running-a-mongodb-replica-set-on-docker-1-12-swarm-mode-step-by-step-a5f3ba07d06e

docker node update --label-add mongo.replica=1 $(docker node ls -q -f name=gitlab-irex)
docker node update --label-add mongo.replica=2 $(docker node ls -q -f name=aisop_2_0)
docker node update --label-add mongo.replica=3 $(docker node ls -q -f name=ubuntu)

docker network create --driver overlay --internal mongo

docker volume create --name mongodata1
docker volume create --name mongoconfig1
docker volume create --name mongodata2
docker volume create --name mongoconfig2
docker volume create --name mongodata3
docker volume create --name mongoconfig3



- example

docker service create -e TZ=Asia/Seoul --replicas 1 --network mongo --mount type=volume,source=mongodata1,target=/data/db --mount type=volume,source=mongoconfig1,target=/data/configdb --constraint 'node.labels.mongo.replica == 1' --name mongo1 mongo:3.2 mongod --replSet incsedb
docker service create -e TZ=Asia/Seoul --replicas 1 --network mongo --mount type=volume,source=mongodata2,target=/data/db --mount type=volume,source=mongoconfig2,target=/data/configdb --constraint 'node.labels.mongo.replica == 2' --name mongo2 mongo:3.2 mongod --replSet incsedb
docker service create -e TZ=Asia/Seoul --replicas 1 --network mongo --mount type=volume,source=mongodata3,target=/data/db --mount type=volume,source=mongoconfig3,target=/data/configdb --constraint 'node.labels.mongo.replica == 3' --name mongo3 mongo:3.2 mongod --replSet incsedb
docker service inspect --pretty mongo1


- 
docker exec -it $(docker ps -qf label=com.docker.swarm.service.name=mongo1) mongo --eval 'rs.initiate({ _id: "incsedb", members: [{ _id: 1, host: "mongo1:27017" }, { _id: 2, host: "mongo2:27017" }, { _id: 3, host: "mongo3:27017" }], settings: { getLastErrorDefaults: { w: "majority", wtimeout: 30000 }}})'

docker exec -it $(docker ps -qf label=com.docker.swarm.service.name=mongo1) mongo --eval 'rs.status()'
- example
docker service create --env 'MONGO_URL=mongodb://mongo1:27017,mongo2:27017,mongo3:27017/koalab?replicaSet=example' --name koalab --network mongo --replicas 2 --publish 8083:8080 kalahari/koalab

sudo docker run -p 53160:53160 \
    -p 53160:53160/udp -p 58846:58846 \ 
    -p 8112:8112 -t -i aostanin/deluge /start.sh
EXPOSE 8285:8285/udp    
https://stackoverflow.com/questions/27596409/how-do-i-expose-a-udp-port-on-docker

<entry key="mrdb.database.name">incsedb</entry>

http://gitlab.irexnet.co.kr:9000 -> 
http://gitlab.irexnet.co.kr
``` 
# 