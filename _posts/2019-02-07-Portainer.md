---
layout: post
title:  포테이너 설치 하기  
categories:
  - Tdd
tags :   
  - management 
  - node 
---
# 설치 환경 
Ubuntu 18.04 기반 Docker-CE 환경에서 S/W를 설치 한다.  Ubuntu 18.04 기반 Docker-CE 설치 방법은 참고 문헌 Portainer설치법 링크에 나와 있다.    

## Portainer 설치 
 
 - Portainer로  현재 내 서버의 컨테이너 이미지 관리를 web UI로 간편하게 관리 할수 있다. 상세 내용은 [Portainer 프로젝트](https://github.com/portainer/portainer)를 참고한다.
 - 특징은 도커 이미지 관리, 컨테이너 상태 관리, 컨테이너 템플릿 실행 기능이 들어가 있다. 작은 규모 컨테이너를 운영중이라 해당툴을 설치해 컨테이너 관리로 사용할 예정이다. 
 - Command 
 ```bash
 docker volume create portainer_data
 docker run -d --restart=always --name portainer -p 9001:9000 -v /var/run/docker.sock:/var/run/docker.sock -v /home/volumes/portainer_data:/data portainer/portainer:1.20.1
 ```
 - 설치 결과 
 
| ![dashboard](/assets/swinstall/dashboard.png) |
|:--:|
| 1. 대쉬보드 화면  |

| ![template](/assets/swinstall/template.png) |
|:--:|
| 2. 도커 템플릿 화면  |

| ![container](/assets/swinstall/container.png) |
|:--:|
| 3. 컨테이너 관리 화면  |

| ![hostinfo](/assets/swinstall/hostinfo.png) |
|:--:|
| 4. 설치된 호스트 정보 화면  |
 
 - Web으로 서버의 컨테이너 관리 할수 있으며, 필요시 터미널 접속도 지원한다. 간단한 작업는 웹페이지에서 컨테이너 터미널 접속해 처리가 가능하다. 다양한 서버 이미지 템플릿을 제공하고 
 있어 필요시 생성해서 사용한다.
    
 
# 참고문헌 
 - [Portainer설치법](https://clouding.io/kb/en/install-portainer-on-ubuntu-18-04)
 
 

