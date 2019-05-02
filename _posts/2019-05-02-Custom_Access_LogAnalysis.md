---
layout: post
title:  엘라스틱 스택을 활용한 JEUS 엑세스 로그 분석   
categories:
  - ElasticSearch
tags :   
  - Es_Stack    
---

# 1. JEUS 로거 샘플 포맷    
```
JEUS 샘플 액세스 로그   
[2019.5.2 13:42:55] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.15.000044/cnt-0.2.481.1.100.15.000044/la" 200 767ms
[2019.5.2 13:42:55] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.12.00168A/cnt-0.2.481.1.100.12.00168A/la" 200 932ms
[2019.5.2 13:42:56] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.15.000037/cnt-0.2.481.1.100.15.000037/la" 200 656ms
[2019.5.2 13:42:56] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.15.000064/cnt-0.2.481.1.100.15.000064/la" 200 625ms
[2019.5.2 13:42:56] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.13.30013C/cnt-0.2.481.1.100.13.30013C/la" 200 646ms
[2019.5.2 13:42:58] 192.168.102.3 "GET /cse/orange/SAE-0.2.481.1.100.13.300149/cnt-0.2.481.1.100.13.300149/la" 404 48ms

JEUS 샘플 액세스 
[시간] %{클라이언트IP} "%{HTTP METHOD} %{URLPATH}" %{HTTP STATUS CODE} %{Elapsed Time}
 - 시간 : 년.월.달 시간:분:초를 의미 하며 요청한 시간을 의미
 - 클라이언트IP : 요청한 클라이언트 IP
 - HTTP METHOD : HTTP Spec Request Method 
 - HTTP STATUS CODE : HTTP Spec Response Status Code 
 - Elapsed Time : 요청 처리 소용 시간을 의미 단위/ ms(Milisecond)     
```
 - 목표 
    - JEUS 액세스 로거로 남은 메세지를 라인당 파싱하여 엘라스틱 저장소에 넣고 키바나로 가시화 된 차트로 분석 한다. 

# 2. 엘라스틱 스택 파이프 라인 설명 
![pipeline](/assets/catpure/es-custom_access_loganalysis.png)  


 - FileBeat 주의 사항은에서 한번 읽은 로그 파일은 FileBeat에서 읽었던 부분의 대한 정보를 offset로 관리함으로  다시 
 파일 정보를 로드 하기 위해서 FileBeat가 읽어던 파일Offset 정보를 초기화 해야 한다.

# 3. Docker ELK 기동
 - docker 와 docker-compose는 반드시 개인 PC에 설치가 되어 있어야한다. 설치 방법은 아래 사이트 링크에서 확인한다. 
 - docker install 
   - https://docs.docker.com/install/linux/docker-ce/ubuntu/
 - docker-compose Install
   - https://docs.docker.com/compose/install/
      
 ``` 
 
 - 아래와 내용을  docker-compose.yml 로 저장한다. 
version: '3.2'
services:
elk:
  image: sebp/elk:7.0.0
  environment:
   - LOGSTASH_START=0
   - KIBANA_START=1
   - ELASTICSEARCH_START=1
   - TZ=Asia/Seoul
   - ES_HEAP_SIZE=1g
   - LS_HEAP_SIZE=2g
  ports:
   - 5601:5601
   - 9200:9200
   - 5000:5000
  volumes:
   - elk-data:/var/lib/elasticsearch 
volumes:
elk-data:
  external: true     
 ```
 - 이제 아래 CLI 기반으로 elk 기동 한다.
  
 
 ```bash
# 영구 볼륨 생성  
docker volume create elk-data
# docker elk 기동
# 주의 사항은 docker-compose.yml 파일이 있는곳에서 반든시 실행 한다.  
docker-compose up -d
 ```
# 4. index 텀플릿 설정
 - 터미널 아래 명령어 엘라스틱 서치 템플릿 설정 실행  
```
curl -v -XPUT -H "Content-type: application/json" -d '{
  "order": 0,
  "index_patterns": "jeus_log-*",
  "mappings": {
    "properties": {
      "geoip": {
        "properties": {
          "location": {
            "type": "geo_point"
          }
        }
      },
      "response": {
        "type": "long"
      },
      "status_code" :{
        "type": "integer",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      }
    }
  }
}' 'http://localhost:9200/_template/access'
``` 
 
 - 성공하면 acknowledge : true 라고 나온다 
 - 이제 ElasticSearch에 넣을 준비가 끝났다  
 - 템플릿 작업은 DB와 비유 하자만 DB 테이블 데이터 타입을 미리 정의 하는것 과 같다. 
   - 위 명령어는 index 패턴이 jeus-log로 시작하는 prefix는 내가 정의한 데이터 정의로 만들어줘 라고 해석 하면 된다.    
 
   
# 5. FileBeat 설치 
 ```
 # curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.0.1-darwin-x86_64.tar.gz
 # tar xzvf filebeat-7.0.1-darwin-x86_64.tar.gz
 # cd filebeat-7.0.1-darwin-x86_64
  ```
 - filebeat.yml 파일 내용에 아래와 같이 설정한다.  

```bash
#=========================== Filebeat inputs =============================
filebeat.inputs:
- type: log
  # 로그 분석 하고자 하는 곳의 디렉토리 및 로그 파일 패턴을 입력 하고 저장한다.  
  paths:
    - /home/kranian/project/elk/logs/*.log 
#============================= Filebeat modules ===============================

filebeat.config.modules:
  # Glob pattern for configuration loading
  path: ${path.config}/modules.d/*.yml

  # Set to true to enable config reloading
  reload.enabled: false
#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["127.0.0.1:5044"]

#================================ Processors =====================================

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~

#================================ Logging =====================================
logging.level: debug
logging.selectors: ["*"]
```    

 - 설정 변경 후 filebeat 기동
  
```
./filebeat -e -c filebeat.yml -d publish 
```

 - 참고사항으로 filebeat 에서 로그파일 읽었던 Fileoffset을 리셋 하고 있을 경우 $FILE_BEAT_HOME/data/registry 폴더을 삭제한다. 
 
# 6. logStatsh 설치
 
 ```
 # curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-7.0.0.tar.gz
 # tar xzvf logstash-7.0.0.tar.gz
 # cd logstash-7.0.0
 # cd bin 
```  
 - logstash 설정 파일을 아래와 같이 생성 한다.
  
``` 
# Sample Logstash configuration for creating a simple
# Beats -> Logstash -> Elasticsearch pipeline.

input {
  beats {
    port => 5044
  }
}

filter {
  grok {
    match => { "message" => ["\[%{YEAR:year}.%{MONTHNUM:month}.%{MONTHDAY:day} %{HOUR:hour}:%{MINUTE:minutes}:%{SECOND:second}\] %{IP:clientip:ip} \"%{WORD:method} %{URIPATHPARAM:request}\" %{NUMBER:status_code} %{NUMBER:response}"] 
    }
  }
  geoip {
    source => "clientip"
    target => "geoip"
  } 

  mutate {
        add_field => { 
            "timestamp" => "%{year}.%{month}.%{day} %{hour}:%{minutes}:%{second}"
        }
  } 
  date {
       match => [ "timestamp" , "yyyy.MM.dd HH:mm:ss" ]
       locale => "ko"
  }
  mutate {
      remove_field => [ "year" ,"month","day","hour","minutes","second"]
  } 
  
}

output {
  stdout { codec => rubydebug }
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "jeus_log-%{+YYYY.MM.dd}"
   #user => "elastic"
   #password => "changeme"
  }
}
``` 

 - logstash.conf 파일로 저장 하고 기동 
 
```bash
 
./logstash -e -c logstash.conf
```  

# JEUS 로그 기반 으로 가시화
  
![log-analysis1](/assets/catpure/kibana-jeus-log-analysis.png)
 - 사용자 요청 및 방문자 수 가시화  
 - 응답 시간 분포도 확인 가시화 
 - 사용자 위치 확인 가시화 
 
![log-analysis2](/assets/catpure/kibana-jeus-log-analysis-map.png) 
 - 지도에서 사용자 위치 확인
 
- 차트 만드는 방법도 가이드 하고 싶으나 시간 관계상 여기는 넣지 않는다. 
 - 차트 만드는 방법은 아래 링크를 확인한다. kibana5이나 6는 차트 생성 방법이 동일하다.아래 링크를 통해 강의듣고 만들어 보아라    
   - https://www.youtube.com/watch?v=xPjNtd8xUZo
     
# 참고 
 - docker-compose 설정 파일고 filebeat, logstash 설정 했던 파일들 github에 올려 놓는다. 
   - https://github.com/kranian/es-jeus-log-analysis
 

