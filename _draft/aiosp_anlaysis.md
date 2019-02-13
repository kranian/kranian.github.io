---
layout: post
title: "Edge Case: Many Tags"
categories:
  - Edge Case
tags:
  - content
  - css
  - edge case
  - html
  - layout
  - title
last_modified_at: 2017-03-09T14:10:02-05:00
---  
# 1. Registration   
[reg](/assets/gwp/1.reg.png) 
 
``` 
@startuml
    [gwp] <-> [mobius]
    note right of [mobius]
        <csr> GWP 등록  
    end note        
    note left of [gwp]
        <remotecse> mobius 등록  
    end note
@enduml  
```

# 2. DMR 
[dmr](/assets/gwp/2.dmr.png)
```     
@startuml  
    [GWP] -> [mobius]
    note right of [mobius]
        GWP를 통해 Mobius <container> 생성,조회,삭제,수정  
          - rn: cnt-gwp 등록
          - url : [gwp주소]/~/mobius/mobius/cnt-DMR  
    end note
@enduml
```

# 3. Discovery 
[dis](/assets/gwp/3.dis.png)
```  
@startuml  
    [GWP] -> [Mobius]
    note right of [mobius]
        GWP를 통해 Mobius <container> Discovery
        - rn: cnt-gwp  Discovery
        - GWP : url : [gwp주소]/~/mobius/mobius?fu=1&ty=3   
    end note
@enduml
```

# 4. Group
[grp](/assets/gwp/4.group.png) 
```     
@startuml  
    [GWP] -> [Mobius]
    note right of [mobius]
        GWP를 통해 Mobius <Group> 생성
        - rn: grp-gwp 
        - GWP : url : [gwp주소]/~/mobius/mobius   
    end note
@enduml
```
# 5. Subscription & Notification
[subs](/assets/gwp/5.subs.png)
```
@startuml  
    [GWP] -> [Mobius] : 1. subscription 생성, 2.cin 생성   
    [mobius] -> [ae] : 3. notify
    note left of [mobius]
        GWP를 통해 Mobius <sub> 생성 
        - rn: sub-gwp 
        - GWP : url : [gwp주소]/~/mobius/mobius/cnt-gwp   
    end note
@enduml
```
# 6. Access Control Policy
[acp](/assets/gwp/6.acp.png)
```
@startuml  
    [GWP] -> [Mobius] : 1. acp 생성     
    note right of [mobius]
        GWP를 통해 Mobius <acp> 생성 
        - rn: acp-gwp 
        - GWP : url : [gwp주소]/~/mobius/mobius
    end note
@enduml
```


```
@startuml
actor 유저   
    유저 -> 통합플랫폼UI : 1. 브라우저 접속
    통합플랫폼UI -> 통합플랫폼UI : 2. 사용자 브라우저 언어 자동감지        
    통합플랫폼UI -> 통합플랫폼UI서버 : 3. 사용자 언어팩 요청  
    통합플랫폼UI <- 통합플랫폼UI서버 : 4. 사용자 언어팩 응답 
    통합플랫폼UI -> 통합플랫폼UI : 5. 언어팩 로딩 
    통합플랫폼UI -> 통합플랫폼UI : 6. 언어팩 렌더링 
    
@enduml
```


```
@startuml 
   node GWP 
   node 통합플랫폼UI      
  통합플랫폼UI<-GWP:응답시간 3초 이내  
@enduml

@startuml 
   node SwitchHub
   node GWP 
   node 통합플랫폼UI
   
   통합플랫폼UI-SwitchHub
   SwitchHub-GWP        
   
   note top of 통합플랫폼UI
      IP: 192.168.0.3         
      서비스 주소: http://192.168.0.3:8087
   end note
   
   note top of GWP
      IP: 192.168.0.2
      서비스 주소: http://192.168.0.2:8080      
   end note
@enduml
```   


```
```
@startuml
== 컴포넌트 생성 ==
 Topology->Topology: constructor  
 Topology->Topology: componentWillMount
 Topology->Topology: render
 Topology->Topology: componentDidMount
== 컴포넌트 prop 값 체인지 이벤트 ==  
 Topology-[#0000FF]->Topology: componentWillReceiveProps 
 Topology-[#0000FF]->Topology: shouldComponentUpdate
 Topology-[#0000FF]->Topology: componentWillUpdate
 Topology-[#0000FF]->Topology: render
 Topology-[#0000FF]->Topology: componentDidUpdate
@enduml 

@startuml
node TMTC
node GWP
node 데이터통합관제서비스UI

TMTC->GWP : 데이터전송
GWP->데이터통합관제서비스UI 


@enduml

```      
@startuml
node Mobius
node GWP

Mobius-up-Http1
GWP-up-Http2

Http2-left->Http1:1.등록
Http1-left->Http2:2.등록
@enduml 
```

```
@startuml
node AE
node GWP
AE->Http:조회
GWP-left-[Http]:접근권한실행

@enduml
```

