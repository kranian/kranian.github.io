---
layout: post
title:  엘라스틱 스택을 활용한 JEUS 엑세스 로그 분석   
categories:
  - ElasticSearch
tags :   
  - Es_Stack    
---
# 0. 요구 사항 
 - 아래 작업을 진행을 이해 하기 위해서는 엘라스틱 스택에 대한 사전 지식이 필요하다.
 - 데이터 분석 엔지니어 나 장애 분석 엔지니어 라면 엘라스틱 스택에 대해 배울만한 가치가 있다고 생각한다.    
 
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
 - 아래와 내용을  docker-compose.yml 로 저장한다.
        
 ```  
version: '3.2'
services:
 elk:
  image: sebp/elk:700
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
 
 ```
 - 이제 아래 CLI 기반으로 elk 기동 한다.
 - 아래 명령어를 터미널에 입력하자  
 
 ```bash
# docker elk 기동
# 주의 사항은 docker-compose.yml 파일이 있는곳에서 반드시 실행 한다.  
docker-compose up -d

# 그려면 기동 되는것을 확인 할수 있다. 

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
   - 이 템플릿 작업은 DB와 비유 하자만 한 데이터 로우의 데이터 타입을 미리 정의 하는것 과 같다.        
 - 만약 이작업을 않하면 geo location 정보를 엘라스틱에서 타입을 알수 없어 다이나믹 데이터 포맷으로 자동 인식 된다. 
   
# 5. FileBeat 설치 
 ```
# 맥용 파일 비트 다운로드 
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.0.0-darwin-x86_64.tar.gz
# 리눅스용 다운로드 
https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.0.0-linux-x86_64.tar.gz

# 압축 해제 
tar xzvf filebeat-7.0.0-darwin-x86_64.tar.gz
# 파일비트 설치 경로 이동 
cd filebeat-7.0.0-darwin-x86_64
  ```
 - filebeat.yml 파일 내용에 아래와 같이 설정한다.  

```bash
#=========================== Filebeat inputs =============================
filebeat.inputs:
- type: log
  # 로그 분석 하고자 하는 곳의 디렉토리 및 로그 파일 패턴을 입력 하고 저장한다.  
  paths:
    - /home/kranian/project/elk/logs/*.log # 로그 디렉토리 설정 및 패턴 설정, wildcard 패턴을 사용하여 로그를 입력하자

  # tail_files: true # filebeat 시작시점 기준 파일 끝에서부터 로깅을 읽기 시작
  # ignore_older: 1m # filebeat 시작시점 기준 1분전의 내용은 무시        

#----------------------------- Logstash output --------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["127.0.0.1:5044"]

#================================ Logging =====================================
logging.level: debug
logging.selectors: ["*"]
```    

 - 설정 변경 후 filebeat 기동
  
```
./filebeat -e -c filebeat.yml -d publish 
```

  - 위와 같이 설정파일을 작성한 다음 아래처럼 실행을 하면 엑세스 파일의 내용이 filebeat를 거치고 logstash를 거쳐 최종적으로 elasticsearch 에 도달하게 된다. 기존에 엑세스 로그가 양이 많다면 그 정보를 다 읽는 시간이 걸리므로 주의한다. (filebeat 자체적으로 해당 파일의 offset을 관리하기 때문)
 - 파일의 offset 관리는 $FILE_BEAT_HOME/data/registry 관리 한다. 
 - 만약 리로드 하고 싶자면 registry 폴더를 삭제 한다.    
 
# 6. logStatsh 설치
 
 ```
# Java 기반 logstash라 Any Platform 임 다운로드   
curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-7.0.0.tar.gz
# 압축 해제 
tar xzvf logstash-7.0.0.tar.gz
cd logstash-7.0.0
cd bin 
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

# grok 필터를 활용하여 엑세스로그 한줄을 아래처럼 파싱하겠다는 의미
# 해당 필터는 JEUS 로깅 설정에 의해 만들어지는 파일의 포멧에 맞추어 설정해야한다.
filter {
  grok {
    match => { "message" => ["\[%{YEAR:year}.%{MONTHNUM:month}.%{MONTHDAY:day} %{HOUR:hour}:%{MINUTE:minutes}:%{SECOND:second}\] %{IP:clientip:ip} \"%{WORD:method} %{URIPATHPARAM:request}\" %{NUMBER:status_code} %{NUMBER:response}"] 
    }
  }
# IP 기준으로 geo location을 변환한다. 
# logstash에 내용 엔진에 해당 내용을 파싱한다.   
  geoip {
    source => "clientip"
    target => "geoip"
  } 
# 파싱 한 결과를 시간으로 변환하여 필드로 변환 하여 저장한다.  
  mutate {
        add_field => { 
            "timestamp" => "%{year}.%{month}.%{day} %{hour}:%{minutes}:%{second}"
        }
  } 
# timestamp 포맷을 설정한다.   
  date {
       match => [ "timestamp" , "yyyy.MM.dd HH:mm:ss" ]
       locale => "ko"
  }
  mutate {
      remove_field => [ "year" ,"month","day","hour","minutes","second","tags"]
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
./logstash  -e -c logstash.conf
```   
 - 메시지 파싱을 디버깅 하고 싶을 경우 kibana 개발자 도구의 GROK 디버거를 사용하자 
   - https://www.elastic.co/guide/en/kibana/current/grokdebugger-getting-started.html
 - 아래 그림은 JEUS 메시지 파싱 디버깅 결과다  
![grok-debuger](/assets/catpure/log-grok-debuger.png)


 - 잠시 후 logstash에 JEUS 로그가 적재 되면서 Elasticsearch 에 적재가 된다. 
 - 키바나의 매니지먼트 화면에서 Elasticsearch index management 에서 jeus_log-2019.04.30 패턴이 생성되었지 확인 한다. 
 
# 7. Kibana 에서 가시화 작업 진행
  
 - 해당 부분은 Kibana 교육 영상으로 대체 한다. 
 - 차트 만드는 방법은 아래 링크를 확인한다. kibana5이나 6는 차트 생성 방법이 동일하다.아래 링크를 통해 강의듣고 만들어 보아라    
   - https://www.youtube.com/watch?v=xPjNtd8xUZo
          
# 8. 최종 JEUS 로그 기반 으로 가시화 
    
![log-analysis1](/assets/catpure/kibana-jeus-log-analysis.png)
 - 사용자 요청 및 방문자 수 가시화  
 - 응답 시간 분포도 확인 가시화 
 - 사용자 위치 확인 가시화 
 
![log-analysis2](/assets/catpure/kibana-jeus-log-analysis-map.png) 
 - 지도에서 사용자 위치 확인 
 - 차트 만드는 방법도 가이드 하고 싶으나 시간 관계상 여기는 넣지 않는다. 
 - 나는 차트 만들때 https://demo.elastic.co/app/kibana 에 web traffic 대쉬보드를 참고 해 만들었다.
 
![log-visual-ref](/assets/catpure/jeus-log-visual-ref.png)  
      
# 참고 
 - docker-compose 설정 파일고 filebeat, logstash 설정 했던 파일들 github에 올려 놓는다. 
   - https://github.com/kranian/es-jeus-log-analysis
 - [누구나 할 수 있는 엑세스 로그 분석 따라 해보기 (by Elastic Stack)](https://taetaetae.github.io/2019/02/10/access-log-to-elastic-stack/)

