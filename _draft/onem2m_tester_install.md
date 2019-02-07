---
layout: post
title: oneM2MTester 설치 하기 
categories:
  - test
tags:
  - onem2m 
---


# 1. 설치 환경 정보
![os](/assets/tester/osinfo.png) 

```bash 
# 환경 정보
OS : Ubuntu 18.04 x86_64 , Linux 
```  
# 2. oneM2MTester Runtime Environment
테스트 환경을 설정 하기 하기 다음 4가지 설정이 필요하다. 
 1. Java 1.8 설치 
 2. Eclipse 와 plugins install
 3. Titan Core install  
 4. Configurations : Eclipse 설정 
 
## 2.1. Java JDK 인스톨

```bash 
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-8-jdk
``` 

### 2.2 Eclipse 와 Titan plugins install
![eclise](/assets/tester/eclipse_download.png)
 - eclipse ide 2018-12 설치 
 - 인스톨러에서 java developer 플러그인 설치 
  
  
![eclise](/assets/tester/titan_plugin_download.png)
 - 이클립스 타이탄 플러그인 설치 : 6.5.0 설치 

## 2.3 Titan Core install 
![titan](/assets/tester/titan_download.png)
 - 6.4.0 소스 다운로드 후 소스 컴파일 
 
```bash
# 타이탄 코어 소스 에 필요한 컴파일 도구 설치 
sudo apt install build-essential
sudo apt-get install automake
sudo apt-get install g++ expect libssl-dev libxml2-dev libncurses5-dev flex bison
sudo apt-get install xutils-dev
sudo apt-get install ant xsltproc

# 빌드 소스 환경 만들기   
mkdir ~/titan.core

# 빌드 소스 다운로드  
wget https://github.com/eclipse/titan.core/archive/6.4.0.tar.gz

# 타이탄 소스 풀기 
tar xvfz xvf 6.4.0.tar.gz -C ~/titan.core/

# 빌드 하기 위해 타이탄 소스로 이동  
cd ~/titan.core/titan.core-6.4.0

# 빌드 바이너리 개인 설정 추가 
~/titan.core/titan.core-6.4.0$ cat > Makefile.personal
TTCN3_DIR := /home/jskim/titan.core/Install  
XMLDIR := /usr
OPENSSL_DIR := /usr
JNI := no

# 빌드 
~/titan.core/titan.core-6.4.0$ make -j 24
~/titan.core/titan.core-6.4.0$ make all 
~/titan.core/titan.core-6.4.0$ make install 

# 빌드 성공 후 확인  
~/titan.core/titan.core-6.4.0/Install# ls -lt
drwxr-xr-x 3 xxx xxx 4096 Jan 31 09:40 man
drwxr-xr-x 4 xxx xxx 4096 Jan 31 09:40 include
drwxr-xr-x 2 xxx xxx 4096 Jan 31 09:40 lib
drwxr-xr-x 5 xxx xxx 4096 Jan 31 09:40 etc
drwxr-xr-x 2 xxx xxx 4096 Jan 31 09:40 bin
drwxr-xr-x 5 xxx xxx 4096 Jan 31 09:40 help
drwxr-xr-x 2 xxx xxx 4096 Jan 31 09:40 demo

# 타이탄 코어 실행을 위한 환경 설정 추가  
~/.bashrc 파일 안에 다음 환경 설정 추가 
...
export TTCN3_DIR=/home/jskim/bin/titan.core
export PATH=${TTCN3_DIR}/bin:${PATH} 
export LD_LIBRARY_PATH=${TTCN3_DIR}/lib:${LD_LIBRARY_PATH}
.. 
```  

## 2.4 이클립스 환경 설정  
- 이클립스 에서 타이탄 코어 설치 위치 설정 
![titan_config](/assets/tester/eclipse_titan_config.png)

## 2.5 oneM2Mtester 테스터 소스 Eclipse에 import 
```
# oneM2Mtester 소스 다운로드
git https://github.com/IoTKETI/oneM2MTester.git 

# local eclipse workspace에 해당 소스 workspace에 import  
```

## 2.6 Eclipse에서 자동 빌드  
![onem2m_titan_config](/assets/tester/onem2m_tester_builder.png)


```
@startuml
MainController -> MainTestComponent: 생성 ( cmd : cmtc) 
...
HostController -> MainController:  호스트 컨트롤러 실행  ex) oneM2MTester 127.0.0.1 9580 
MainController -> MainTestComponent: 테스트 케이스 모듈 컨트롤러 실행  ex)smtc modulename.testcase_id
MainTestComponent->시험기 : 테스트 케이스 실행
...
MainTestComponent<-시험기 : 실행 결과 취득 후 Pass 여부 체크 
   
@enduml
```