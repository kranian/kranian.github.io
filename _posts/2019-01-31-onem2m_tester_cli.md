---
layout: post
title: IoTKETI/oneM2MTester CLI 모드로 실행 하기 
categories:
  - onem2m
tags:
  - test
  - eclipse
  - java  
---

### 1 CLI Mode 빌드 
```bash
# 테스트 케이스 소스 생성 
jskim@aisop_2_0:~/IdeaProjects$ git https://github.com/IoTKETI/oneM2MTester.git
jskim@aisop_2_0:~/IdeaProjects$ cd oneM2MTester 
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester$ makefilegen -f -g -t oneM2MTester.tpd

# 테스트 케이스 빌드 
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester$ cd bin 
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester/bin$ make
# 테스트 케이스 빌드 성공 후 바이너리 생성된다. 
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester/bin$ ls -ltr  
...
-rw-rw-r-- 1 jskim jskim  2485944 Jan 31 16:54 OneM2M_Testcases_CSE_Release_1.o
-rw-rw-r-- 1 jskim jskim 63010152 Jan 31 16:56 OneM2M_Types.o
-rwxrwxr-x 1 jskim jskim 52715504 Jan 31 16:56 oneM2MTester <--- 이파일이 생성된다. 

# 해당파일 실행 하면 테스트 케이스 목록이 조회됨
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester/bin$ ./oneM2MTester -l
...
OneM2M_Testcases_CSE_Release_2.TC_CSE_SUB_UPD_008
OneM2M_Testcases_CSE_Release_2.TC_CSE_SUB_UPD_009
OneM2M_Testcases_CSE_Release_2.TC_CSE_SUB_DEL_002
OneM2M_Testcases_CSE_Release_2.TC_CSE_SUB_DEL_003
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_ACP_UPD
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_ACP_CRE
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_CNT_UPD
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_CNT_CRE
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_GRP_UPD
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_GRP_CRE
OneM2M_Testcases_CSE_Release_2.TC_CSE_ANNC_001_CIN_CRE

# 위 목록은 타이탄 마스터 컨트롤에서 하나씩 테스트케이스를 실행 할때 필요함
# 빌드는 한번만 하여 생성 하면 종료 된다.  
```
### 2. CLI Mode 실행 
![runtime](/assets/tester/mc_cli_mode.png)

```bash

1. 마스터 컨트롤 환경 설정 기반으로 실행  
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester$ mctr_cli oneM2MTester/Configs/oneM2MTesterConfig.cfg 
 

*************************************************************************
* TTCN-3 Test Executor - Main Controller 2                              *
* Version: CRL 113 200/6 R4A                                            *
* Copyright (c) 2000-2018 Ericsson Telecom AB                           *
* All rights reserved. This program and the accompanying materials      *
* are made available under the terms of the Eclipse Public License v1.0 *
* which accompanies this distribution, and is available at              *
* http://www.eclipse.org/legal/epl-v10.html                             *
*************************************************************************

Using configuration file: oneM2MTester/Configs/oneM2MTesterConfig.cfg
MC@aisop_2_0: Unix server socket created successfully.
MC@aisop_2_0: Listening on TCP port 9580.
MC2> cmtc
Waiting for HC to connect...

2. 마스터 컨트롤 접속 
jskim@aisop_2_0:~/IdeaProjects/oneM2MTester/bin$ ./oneM2MTester 127.0.0.1 9580 
TTCN-3 Host Controller (parallel mode), version CRL 113 200/6 R4A
The address of MC was set to a local IP address. This may cause incorrect behavior if a HC from a remote host also connects to MC.

# 주의사항으로 oneM2MTester 실행시 반드시 바이너리 파일이 있는 곳에서 실행 한다. 
# 안그려면 oneM2M 테스트 케이스 실행시 테스트기 에서 보내는 리소스 정보의 속성값들이 shortname 이 변환이 안된다. 
 
MC@aisop_2_0: New HC connected from 127.0.0.1 [127.0.0.1]. aisop_2_0: Linux 4.15.0-43-generic on x86_64.
MC@aisop_2_0: Downloading configuration file to all HCs.
MC@aisop_2_0: Configuration file was processed on all HCs.
MC@aisop_2_0: Creating MTC on host 127.0.0.1.
MC@aisop_2_0: MTC is created.

3. 테스트 케이스 실행
MC2> smtc OneM2M_Testcases_CSE_Release_1.TC_CSE_GEN_RET_001_CSR  
...
MTC@aisop_2_0: Test case TC_CSE_GEN_RET_001_CSR finished. Verdict: pass
MC@aisop_2_0: Test execution finished.

```
## 참고 
 - [oneM2MTester 설치 메뉴얼](https://github.com/IoTKETI/oneM2MTester/blob/master/doc/oneM2MTester_User_Manual/oneM2MTester_User_Guide_ver2_1_0.pdf)