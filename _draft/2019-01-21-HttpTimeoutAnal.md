---
layout: post
title: Http Time Out 현상 분석 
categories:
  - network 
tags :   
  - analysis     
---
## 1. 현상 
![http timeout](/assets/catpure/httptimeout.png)
 - 서비스 클라이언트 에서 oneM2M 서버로 조회 요청 시도 시 timeout 발생 하는 현상이 발생 했다. 
   - http timeout 설정이 10초로 설정 되어 있는 상황  
   - 로그 파일에 1분에 한번씩 SocketTimeoutException 누적되어 있는 상황
   - 아래 예외 상황 메세지의 의미는 서버 쪽에 응답을 10초 이상 주지 않을 경우 발생 하는것으로    
```java
java.net.SocketTimeoutException: timeout
	at okio.Okio$4.newTimeoutException(Okio.java:232)
	at okio.AsyncTimeout.exit(AsyncTimeout.java:285)
	at okio.AsyncTimeout$2.read(AsyncTimeout.java:241)
	at okio.RealBufferedSource.indexOf(RealBufferedSource.java:355)
	at okio.RealBufferedSource.readUtf8LineStrict(RealBufferedSource.java:227)
	at okhttp3.internal.http1.Http1Codec.readHeaderLine(Http1Codec.java:215)
	at okhttp3.internal.http1.Http1Codec.readResponseHeaders(Http1Codec.java:189)
	at okhttp3.internal.http.CallServerInterceptor.intercept(CallServerInterceptor.java:88)
```  
## 2. 분석 진행 
 - 왜 그런건지
 


 