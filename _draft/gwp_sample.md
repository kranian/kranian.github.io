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
```
@startuml
node GWP
node Mobius
GWP->Mobius : 등록
@enduml  

@startuml
node GWP
node Mobius
GWP<-Mobius : 등록
@enduml


@startuml
node AE1
node GWP
node Mobius
node AE2

http->GWP
AE1->http: 생성
GWP->Mobius : 생성
Mobius->AE2 : Notify
@enduml    
```
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
    [GWP] -> [mobius]
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
    [GWP] -> [mobius]
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
    [GWP] -> [mobius] : 1. subscription 생성, 2.cin 생성   
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
    [GWP] -> [mobius] : 1. acp 생성     
    note right of [mobius]
        GWP를 통해 Mobius <acp> 생성 
        - rn: acp-gwp 
        - GWP : url : [gwp주소]/~/mobius/mobius
    end note
@enduml
```