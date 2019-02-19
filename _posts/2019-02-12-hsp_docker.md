---
layout: post
title: Home Water Service 가상화 만들기 
categories:
  - Docker
tags:
  - nodejs
  - mariadb
  - ftp
published: false  
---  
```
- nodejs 8.x damon  
- maria db damon 
  maria db => nodejs 링킹 
- vproftpd damon
  볼륭 ==> nodejs에 마운트 


13.5 db테이블 복원
# mysql -uroot -phsp.admin hsp < sql.txt

# vi /etc/crontab
1 0 * * * root sh /root/HSP_ServerAgent/delete-wq-data-info.sh

# curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -

exports.TOKEN_TIMETOLIVE = 60*60*24; // 24 Hours
exports.TOKEN_LENGTH = 100;
exports.CLIENT_ID_LENGTH = 25;
exports.CLIENT_SECRET_LENGTH = 50;

exports.DB_HOST_NAME = '127.0.0.1';
exports.DB_USER = 'root';
exports.DB_PASSWORD = 'hsp.admin';
exports.DB_NAME = 'hsp';

참고 자료 NODE JS 도커 이미지 만들기 
 - https://nodejs.org/en/docs/guides/nodejs-docker-webapp
 - https://medium.com/oracledevs/from-locally-running-node-application-to-cloud-based-kubernetes-deployment-1fed34280952
```