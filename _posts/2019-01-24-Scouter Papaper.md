---
layout: post
title: 스카우터 페이퍼 토폴로지 그룹핑 텍스트 중복 현상 수정 
categories:
  - github 
tags :   
  - issue,review     
---
[스카우터 페이퍼 이슈 #114](https://github.com/scouter-contrib/scouter-paper/issues/114)
 
  - 지난해 10월경 스카우터 메일링 리스트에서 gunlee01 님이 리뷰 요청하는 글을 봤다. 이슈는 내용은 토폴로지 차트가 움직이면 
중복으로 텍스트가 나타는 현상이었다. 
  - 이전에도 이런 비슷한 경험을 해본 적이 있어 해당 프로젝트에 이슈 분석을 도전을 해봤다.  
  
![issue](/assets/catpure/gihtub_scouter-paper.png)

## 1. 수정 
  - 왜 이런 현상 발생 하는지 재현 테스트를 시도 해봤다.  
   1. 일단 중복으로 텍스트가 나오는 클래스의 이름을 찾아봤다. 
![issue2](/assets/catpure/gihtub_scouter-paper1.png)    
   2.  CSS 클래스명이 .node-inst-count 확인했고 해당 클래스로 조작하는 소스를 찾아 수정 시도했다.      
   3. 의심 되는 대상을 찾아 d3 속성 class 이름 값 .node-inst-count -> node-inst-count 수정        
      
## 2. 원인 
  - 그럼 왜 이런 현상 발생했을까? 이는 간단했다. d3 텍스트 렌더링 속성에 설정을 잘못 해 발생한 것으로 파악됐다. 
  - API 문서를 체크 해 보니  일반적인 설정이 아래와 같았다. 
![issue3](/assets/catpure/gihtub_scouter-paper2.png)  
  
  
## 3. 소스 수정 후 풀 리퀘스 요청
![issue4](/assets/catpure/gihtub_scouter-paper3.png) 
![issue5](/assets/catpure/gihtub_scouter-paper4.png)
 
  - 리퀘스 수락 되어 머지가 되었다. [풀리퀘스트내역 #114](https://github.com/scouter-contrib/scouter-paper/pull/116/files)
  
## 4. 후기  
  - 분석 시간만 소요됐고 그외에 소스 수정은 많지는 않았다.   
  - 항상 느끼는 거지만 이슈 재현만 된다면 문제 되는 지점을 찾는 것은 시간과의 싸움인 것 같다.    
      
## 참고 
  - [github풀리퀘스 하는 방법](https://wayhome25.github.io/git/2017/07/08/git-first-pull-request-story)  
     