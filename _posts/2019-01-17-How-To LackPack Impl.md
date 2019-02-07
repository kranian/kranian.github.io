---
layout: post
title: 흔한 언어팩 만들기 
categories:
  - FrontEnd
tags :   
  - Language     
---
## 1. 지역화 하기 위해 if문을 이용한 언어설정  
 
```javascript
<script>
    boolean isEnglish = "en-US" === "브라우저 언어 감지 값"; 
    message = en ? "MESSAGE" : "메세지";
    alert(message);
</script>
```
 - 장점 : 빠르게 언어 팩 설정을 할수 있음 
 - 단점 : 다른 언어의 확장성이 없고, 소스에 들어가 있기 때문에 수정시 오타를 주의 해야 한다. 
 - 내 경험을 빌리자면 소스 수정 자체가 괴롭다. 
  

## 2. 파일 방식의 지역화
 
```
file 명 : en.message 
message=MESSAGE

file 명 : ko.message 
message=메세지
```

 - 장점 : 규격화된 메시지를 파일 형태로 로컬 파일에 저장 
 - 단점 : 언어 별 메시지 방식이기 때문에 혼자 만들때는 적합 하지 않을지도 모른다. 관리 언어 파일이 많아 지면 언어 코드 관리 혼자 감당 하기 
 힘들다.  
 - 오픈 소스가 해당 방식을 이용하고 있다. 


## 3. DB 방식의 지역화

```
- format : 
{
 '언어팩 코드' : '메세지'
}
- 구현 
{
 'T_ALERT_USER_MESSAGE' : 'MESSAGE'
}

DB에 저장시에는 기본 언어 코드를 en으로 저장 

기본 메시지 코드를 프론트엔드에 심고 프론트엔드에 적용 
```
 
DB 레코드 셋을 언어코드,메시지 코드, 번역 구조화하고 저장  
 - 장점 : 기본 언어 팩 설정 후 다른 언어 확장이 쉬움 
 - 단점 : DB 응답 늦으면 사용하기가 어렵다. 
 혼자 진행할 때 이 방식을 사용했는데 맨 처음 기본 언어 셋을 실제 적용 할때만 상세하게 다시 체크 했다. 
 그 이후부터는 구글 번역기와 구글 엑셀 시트에 활용해 지역화를 만들었다. 
 

- 저장 예시
![Langpack](/assets/catpure/langpack_list1.png)
- 언어 별 가공 후  예시 
![Langpack](/assets/catpure/langpack_list2.png)

  


