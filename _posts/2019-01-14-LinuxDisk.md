---
layout: post
title: 리눅스 디렉토리 용량 검색  
categories:
  - Linux
tags :   
  - Disk Search    
---
 - 목적 
   - 리눅스 특정 디렉토리의 용량을 검색 하고 있을 경우 사용함
 - 사용 계기 
   - 개발계 서버의 / 디렉토리 용량이 10% 내외로 남아 어떻 디렉토리에서 용량이 차지 했는지 검색 하기 위해 사용         
        
``` bash
# du 옵션 
# -s separate-dirs  subdirectories 별 사이즈 출력   
# -h human-readable format print
# 상세 내용는 man du 로 확인 가능함 
 
# du 옵션 기반으로 리눅스 디렉토리 검색 스크립트 내용 
for i in $(ls -d target/*/)
do 
  du -sh $i
done

# 설명 
# 검색을 원하는 디렉토리를 설정 하여 스크립트를 실행하면 아래와 디렉토리 용량을 확인 할수 있음 

# 결과 예시 
5.2M   target/ami/
445M   target/aThings/
121M   target/backup/
544K   target/boot/
12K    target/cert/
4.0K   target/Desktop/
4.4G   target/docker/
4.0K   target/Documents/
4.0K   target/Downloads/
445M   target/jdk/
8.1M   target/lib/
12K    target/logs/
4.0K   target/Music/
54M    target/oneXXXX1/
450M   target/oneXXXX2/
1.4T   target/oneXXXX3/ <---- 가장 큰 용량 확인 
4.0K   target/Pictures/
1.3M   target/plugin/
1.6G   target/pptx/
4.0K   target/Public/
42G    target/ref/
54M    target/ROOT/
11M    target/scouter/
8.0K   target/script/
7.0M   target/shiro-root-1.3.2-source-release/
``` 
 - 참고 문헌   
   - [제타위키 DU 관련](https://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC_%EC%9A%A9%EB%9F%89_%ED%99%95%EC%9D%B8_du)              

