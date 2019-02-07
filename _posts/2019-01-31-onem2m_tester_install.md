---
layout: post
title: IoTKETI/oneM2MTester 설치 하기 
categories:
  - onem2m
tags:
  - test
  - eclipse
  - java  
---

# 배경               
 - 나는 IoTKETI/oneM2MTester 메뉴얼에 적힌 OS 환경이 달라 이력 사항으로 설치 이력을 아래와 같이 남긴다.    
 참고사항으로 IoTKETI/oneM2MTester 메뉴얼에 적힌 환경 정보는 **Ubuntu 16.04** 기반이며 Titan Core 바이너리 또한 Ubuntu 16.04 기반만 배포 하고 있다. 
 만약 처음 설치 하시는 유저는 Ubuntu 16.04에 하는걸 권고한다.  
 
 - 해당 툴을 처음 설치하는 분들게 설치에 도움이 되길 희망 한다.
            
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
# 소스 빌드 시간은 CPU 사양이 낮으면 1~2시간 소용된다. 여기에서는 약 10분내외로 컴파일 되었다.    
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

## 2.6 Eclipse 자동 빌드 확인   
![onem2m_titan_config](/assets/tester/onem2m_tester_builder.png)

## 2.7 oneM2M 환경 설정 
```
공식 메뉴얼에는 src > oneM2MTestConfig.cfg 파일을 시험기 환경 설정에 맞추어 설정 하라고 가이드 하고 있다.   
아래 기본 설정들은 Mobius 에 맞추어 있다.
 
[MAIN_CONTROLLER]
# The options herein control the behavior of MC.
TCPPort := 9580
KillTimer := 10.0

[LOGGING]
LogFile 			:= "../logs/%e.%h-%r.%s"
FileMask 			:= LOG_ALL | DEBUG | ERROR | USER | WARNING | TESTCASE | STATISTICS | MATCHING |VERDICTOP_SETVERDICT
ConsoleMask 		:= TESTCASE | PORTEVENT | ERROR | STATISTICS | MATCHING | VERDICTOP_SETVERDICT | DEBUG
LogSourceInfo 	    := Yes
AppendFile 		    := No
TimeStampFormat 	:= DateTime
LogEventTypes 	    := Yes
SourceInfoFormat 	:= Single
LogEntityName 	    := Yes

[TESTPORT_PARAMETERS]
// mcaPort 
system.mcaPort.defaultListeningPort				:= "0"               // Allow to use specific port number      
system.mcaPort.map_behavior						:= "connect"  
system.mcaPort.RemotePort						:= "7579"	  		 // SUT - Port
system.mcaPort.RemoteHost						:= "192.168.19.147"   // SUT - IP    
system.mcaPort.lazy_conn_id_handling			:= "yes"  
system.mcaPort.debug                            := "yes"
system.mcaPort.map_protocol                     := "tcp"

// mccPort
system.mccPort.defaultListeningPort				:= "0"  
system.mccPort.map_behavior						:= "connect"  
system.mccPort.RemotePort						:= "7579"	  		  // SUT - Port
system.mccPort.RemoteHost						:= "192.168.19.147"   // SUT - IP    
system.mccPort.lazy_conn_id_handling			:= "yes"  
system.mccPort.debug                            := "yes"
system.mcaPort.map_protocol                     := "tcp"

// utPort, this port is used to perform the AE testing.
system.utPort.defaultListeningPort				:= "0"  
system.utPort.map_behavior						:= "connect"  
system.utPort.RemotePort						:= "3000"	  		 // UpperTester - Port
system.utPort.RemoteHost						:= "192.168.19.147"   // UpperTester - IP    
system.utPort.lazy_conn_id_handling				:= "yes"  
system.utPort.debug                             := "yes"  

// mcaPortIn 
system.mcaPortIn.defaultListeningPort			:= "62590"           // Allow to use specific port number      
system.mcaPortIn.map_behavior					:= "listen"   
system.mcaPortIn.debug                          := "yes"
system.mcaPortIn.map_protocol                   := "tcp" 

[MODULE_PARAMETERS]
	
#OneM2M_Constants.tsp_connectionParams 		:=  {"{SUT-SERVER-IP}", Integer_port, "TEST-SYSTEM-IP", Integer_port};
OneM2M_Constants.tsp_connectionParams 		:=  {"192.168.0.153", 7579, "192.168.19.145", 27689};

OneM2M_Pixits.PX_SERIALIZATION 				:= "JSON"; ## json or xml  
-----------------------------> 시험기 데이타 타입 설정 
OneM2M_Pixits.PX_PROTOCOL_BINDING 			:= "HTTP"; ## coap or http or mqtt 
-----------------------------> 시험기 프로토콜 설정 

OneM2M_Pixits.PX_RUN_POSTAMBLE 						:= true;
    
# SUT M2M-Service Provider ID
OneM2M_Pixits.PX_SP_ID								:= "//keti.re.kr"; 
-----------------------------> 시험기 절대 주소체계 설정
   
# Primtive scope could only either be 'e_cseRelative', 'e_spRelative', or 'e_absolute'
OneM2M_Pixits.PX_PRIMITIVE_SCOPE					:= e_cseRelative; 
-----------------------------> 시험기 주소체계 설정 
    
# Addressing format can only either be 'e_hierarchical' or 'e_nonHierarchical'
OneM2M_Pixits.PX_ADDRESSING_METHOD 					:= e_hierarchical; 
-----------------------------> 시험기 주소체계 설정


OneM2M_Pics.PICS_STRUCTURED_CSE_RELATIVE_RESOURCE_ID_FORMAT 	:= true;	  // hierarcahial CSE-relative resource format support declaration
// OneM2M_Pics.PICS_UNSTRUCTURED_CSE_RELATIVE_RESOURCE_ID_FORMAT 	:= false; // set to 'true' only when 'PX_ADDRESSING_METHOD' is set to 'e_nonHierarchical'
    	
OneM2M_Pics.PICS_ACP_SUPPORT 									:= true;
OneM2M_Pics.PICS_IN_CSE 										:= true;	

# Which component do you want to test (IN or MN)?
OneM2M_Pixits.PX_IN_CSE	:= true;
OneM2M_Pixits.PX_MN_CSE	:= false; 	
OneM2M_Pixits.PX_ASN_CSE:= false; 

# Checking utTrigger port for the automated testing
OneM2M_Pixits.PX_UT_IMPLEMENTED := false;
       
# Parameter settings for the SUT
OneM2M_Pixits.PX_CSE_ID						:= "/Mobius";// SP-relative_CSE-ID;   
--------------------> 시험기 CSE ID 설정
OneM2M_Pixits.PX_CSE_NAME					:= "Mobius"; // CSEBase resource name 
--------------------> 시험기 CSE Name 설정 
OneM2M_Pixits.PX_CSE_RESOURCE_ID			:= "Mobius"; // CSE-relative_CSE-ID; 
    
OneM2M_Pixits.PX_SUT_ADDRESS				:= "192.168.35.34:7579"; 
--------------------> IUT 주소 수정 
OneM2M_Pixits.PX_AE1_ADDRESS				:= "192.168.19.147:62590"; // poa for notification url
OneM2M_Pixits.PX_AE2_ADDRESS				:= "192.168.19.147:62590"; // poa for notification url

	
OneM2M_Pixits.PX_AE1_ID_STEM				:= "C_AE_ID_STEM_1"; // AE-ID-STEM has to start 'C' or 'S' 
--------------------> 시험기 설정 
OneM2M_Pixits.PX_AE2_ID_STEM				:= "C_AE_ID_STEM_2"; // AE-ID-STEM has to start 'C' or 'S'
OneM2M_Pixits.PX_APP_ID 					:= "0.2.481.2.0001.001.62590"; 
--------------------> 시험기 설정  
    
OneM2M_Pixits.PX_RESOURCES_TO_BE_DELETED 	:= {"MyAe"}; // {MyAe, MyAe2, ...}
// OneM2M_Pixits.PX_RESOURCE_TO_BE_DELETED 	:= "MyAe";
    
OneM2M_Pixits.PX_SUPER_AE_ID				:= "/Superman"; // Superuser, CSE Minimum Provisioning
OneM2M_Pixits.PX_SUPER_CSE_ID				:= "/Superman"; 

// "all" access control policy is used for a few testcases: 
// TC_CSE_SEC_ACP_004 series, TC_CSE_DMR_UPD_016_CSR_ET, TC_CSE_DMR_UPD_016_CSR_RR
// OneM2M_Pixits.PX_ACOR	 					:= {"Mobius", "/Mobius", "C_AE_ID_STEM_1", "C_AE_ID_STEM_2"}; 
OneM2M_Pixits.PX_ACOR                       := {"all"};

OneM2M_Pixits.PX_ALLOWED_C_AE_IDS := {"C-AllowedAPX_ACOReId"};

# These parameters are used for <remoteCSE>
OneM2M_Pixits.PX_CSE1_ADDRESS				:= "192.168.35.34:7579";
OneM2M_Pixits.PX_CSE1_NAME 					:= "Mobius";
OneM2M_Pixits.PX_CSE1_ID 					:= "/Mobius";

# supportedResourceTypes check of SUT
OneM2M_Pixits.PX_SRT := {int1, int2, int3, int4, int5, int9, int10, int13, int14, int16, int17, int23};
OneM2M_Pixits.PX_POA := {"http://192.168.19.147:7579"};

# Parameter that are not used for the moment (Designed for test location management)
OneM2M_Pixits.PX_IS_LOC_CONTAINER			:= false;
OneM2M_Pixits.PX_XML_NAMESPACE 				:= "m2m=""http://www.onem2m.org/xml/protocols""";
OneM2M_Pixits.PX_LOCATION_TARGET_ID			:= "{LOCATION-TARGET-ID}"; // SUPPOSE TO BE RECEIVED FROM LOCATION SERVER
OneM2M_Pixits.PX_LOCATION_SERVER_ADDRESS	:= "{LOCATION-SERVER-ADDRESS}";	
OneM2M_Pixits.PX_LOCATION_UPDATE_PERIOD		:= "PT10M10S" ;// 10 Minute 10 Seconds
OneM2M_Pixits.PX_LOCATION_CONTAINER_NAME	:= "myLocContainer";
 
```
## 참고 
 - [oneM2MTester 설치 메뉴얼](https://github.com/IoTKETI/oneM2MTester/blob/master/doc/oneM2MTester_User_Manual/oneM2MTester_User_Guide_ver2_1_0.pdf)