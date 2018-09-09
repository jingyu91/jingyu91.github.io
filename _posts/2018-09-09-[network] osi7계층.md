---
title: "OSI 7계층"
category: network
---



OSI7계층의 중요한 목적은 표준과 학습도구이다. 표준화는 다양한 장비 사이에서 같은 규칙을 사용하여 이질성을 감소시킨다.

뿐만 아니라 각각의 층에 대한 프로토콜이 잘 정립되어 있어 해당하는 분야에만 집중 할 수 있다. 예를들어 라우터를 제작할 경우 3계층에 대한 프로토콜만 맞춰어 제작하면 된다.



**Physical - DataLink - Network - Transport - Session - Presentation - Application**

각 계층의 쓰임과 쓰이는 프로토콜에 대해 정리해보았다.



## Application 

사용자 프로그램. 웹이나 FTP같은 유저 인터페이스.

프로토콜: HTTP, DNS, FTP



## Presentation 

데이터의 표현. 암호화/복호화. 운영체제의 한 부분으로 JPEG같은 확장자처럼 전송데이터를 이해할수 있게 번역하는 역할을 수행

프로토콜: JPEG, PNG



## Session

2개의 기기 사이에 세션이 이루어짐. 포트(Port)연결. 	

프로토콜 :SSH, TLS



## Transport

데이터 용량 및 속도 등을 처리. 발신지대 목적지(종단 대 종단)간 제어와 에러를 관리. 패킷들의 전송이 유효한지 확인. 신뢰성 통신을 보장하기도 함 	

프로토콜 : TCP, UDP



## Network

패킷을 발신지부터 목적지까지 전달. 

프로토콜 : IP, ARP

**IP :패킷의 분할/병합기능을 한다.*

**ARP : 패킷에 있는 ip주소를 물리적인 MAC주소로 변환시켜주는 프로토콜이다. DataLink계층으로 전달될때 MAC주소를 프레임으로 캡슐화해서  보낸다.*



## DataLink

두개의 직접 연결된 노드 간 frame 전송. 오류 수정 및 flow control.  	

프로토콜: MAC, PPP



## Phsical

두 노드를 물리적으로 연결시켜주는 방식을 다룸.	

프로토콜 : ethernet