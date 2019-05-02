---
layout: post
title: 개발 읽을 거리 링크        
categories:
  - ElasticSearch
tags :   
  - Es_Stack    
  - link    
  - FrontEnd
published: false  
---

# 개발 노트 
```

PUT twitter 
{}

PUT cse_tx-2019-03-07/_mapping/type 
{
     "properties": {
       "ip": { "type": "ip" },
       "location" : { "type" : "geo_point" },
       "latitude" : { "type" : "half_float" },
       "longitude" : { "type" : "half_float" }
    }
}

GET cse_tx-2019-03-07/_mapping

- 지도 좌표를 그리고 싶어 연구 하던 중... 
https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-put-mapping.html
https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-get-mapping.html
https://www.elastic.co/blog/geoip-in-the-elastic-stack

- 템플릿
결국에는 정교한 컨트롤이 필요한 경우 템플릿 설정을 해야함 ㅠ.ㅠ.
세상에는 꽁짜는 없네 ㅠ.ㅠ  
https://www.elastic.co/guide/en/elasticsearch/client/java-rest/master/java-rest-high-put-template.html
```


```plantuml 
@startuml
 node filebeat[
  <b>FileBeat
  ===
   JEUS 로그 파일 읽어 logstatsh에 전달 한다.       
 ]
 
 node logstash[
   <b>Logstatsh
  ===
  요청 데이터 메시지를 파싱
  .... 
  엘라스틱 저장소에 저장     
 ]
 node elasticsech [
  <b> Elasticsech
  ===
  JEUS 액세스 로그 데이터를 저장한다. 
 ]
 
 node kibana[
 <b> Kibana 
 ===
 엘라스틱 서치에 데이터 가시화 
 ]
 
 filebeat -down-> logstash
 logstash -down-> elasticsech
 kibana -left-> elasticsech
@enduml  
``` 