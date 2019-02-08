---
layout: post
title:  엘라스틱 서치 6.5.5 -> 6.6.0 업그레이드 치트 키  
categories:
  - tdd
tags :   
  - es stack    
---
```
1. Disable shard allocation
curl -X PUT "http://localhost:9200/_cluster/settings" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": "none"
  }
}
'
2. Stop non-essential indexing and perform a synced flush
curl -X POST "localhost:9200/_flush/synced"

3. Stop elastic search 

4. Upgrade the node you shut down.
  - 업그레이드 하기전에 x-pack 사용중지 elasticsearch-plugin remove x-pack

5. Start the upgraded node. 확인 
curl -X GET "localhost:9200/_cat/nodes"

6. Reenable shard allocation.
curl -X PUT "localhost:9200/_cluster/settings" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.enable": null
  }
}
'
9. Wait for the node to recover. (양이 많아 엄청 오래 걸림 약 1시간 걸림)

while true 
 do
 echo "-----------------------------------------------"
 curl -X GET "localhost:9200/_cat/health" -w '\n'
 sleep 1
done 

Wait for the status column to switch from yellow to green. Once the node is green, all primary and replica shards have been allocated.

10. 각 인덱스 별 recovery 확인 
curl -X GET "localhost:9200/_cat/recovery"

```  
 
# 참고 문헌
 - [공식문서 엘라스틱 업그레이드](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-upgrade.html)