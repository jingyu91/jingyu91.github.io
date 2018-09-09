---
title: "TCP와 UDP"
category: network
---



TCP와 UDP는 커널 내부에 구현되어 사용자가 직접 이용하지 못한다. 대신 소켓 인터페이스라는 Transport 계층의 인터페이스를 사용하여 시스템콜 기능으로 호출된다.



**UDP**는 비연결형 프로토콜이다. TCP의 handshaking 과정이 없으며 일방적으로 데이터를 전달하는 통신 프로토콜이다. TCP와 비교하면 안정성은 떨어지지만 속도가 빠르다.



**TCP**는 연결형 서비스를 지원하는 전송계층 프로토콜이다. 전이중 방식의 양방향 통신을 지원하여 두 프로세스가 동시에 데이터를 전송할 수 있다. 연결형 서비스를 지원하기 위해 연결 설정, 데이터 전송, 연결 헤제 3단계를 순차적으로 진행한다.



### 3 way handshaking

연결을 설정할때 이루어진다.



1. 프로세스 A, B가 있다고 가정하면, A는 임의의 번호로 SYN 플래그 비트를 전송하여 연결 설정을 요청한다. (Seq=1000, SYN)
2. 프로세스 B는 A가 보낸 seq번호에 1을 더하여 ACK를 보내고 임의의 seq번호를 보낸다.(Seq=2000 SYN, ACK=1001)
3. A는 B가 보낸 seq번호에 1을 더하여 ACK를 보내면 연결이 성립된다. (ACK=2001)



![그림1](/images/network/3-way-handshake.png)



*SYN : Synchronize sequence number		

*ACK : Acknowledgement



연결이 설정된 이후로는 established 상태로 된다.

 



### 4 way handshaking

연결을 종료할때 이루어진다.



1. A가 할일이 끝나면 FIN을 보내고 A는 FIN_WAIT_1상태로 바뀐다.
2. B는 A가 끝났다는것에 대한 응답 ACK를 보내고 B의 해당 포트를 CLOSE_WAIT로 바꾼다.
3. B가 마무리가 되면 끝났다는 FIN을 A에게 보낸다.
4. A는 B가 끝나다는 것에 대한 응답 ACK를 보낸다. B는 소켓을  CLOSE한다.
5. A는 아직 B로부터 데이터가 받을지 모르기 때문에 일정 시간의 잉여시간을 남겨두는 과정을 거친다. (TIME_WAIT)



![4way_handshake](/images/network/4way_handshake.png)

 



연결이 종료될때에는 예기치 못한 상황이 발생하여 FIN_WAIT상태나 CLOSE_WAIT상태로 남아있을 수 있다.

* **CLOSE_WAIT** 상태 : 어플리케이션에서 close()를 적절하게 처리해주지 못하면, TCP 포트는 CLOSE_WAIT 상태로 계속 기다리게 된다. 이렇게 CLOSE_WAIT 상태가 statement에 많아지게 되면, Hang 이 걸려 더이상 연결을 하지 못하는 경우가 생기기도 한다. 따라서 어플리케이션 개발시 여러 상황에 따라 close() 처리를 잘 해줘야 한다.



* **FIN_WAIT_1** 상태 : FIN_WAIT_1 상태라는 것은 상대방측에 커넥션 종료 요청을 했는데, ACK를 받지 못한 상태로 기다리고 있는 것이다. 이것은 아마 서버를 찾을 수 없는 것으로, 네트워크 및 방화벽의 문제일 수 있다.

  (FIN_WAIT_1 의 상태는 일정 시간이 지나 Time Out이 되면 자동으로 닫는다.)

  

* **FIN_WAIT_2** 상태 : FIN_WAIT_2 상태는 클라이언트가 서버에 종료를 요청한 후 서버에서 요청을 접수했다고 ACK를 받았지만, 서버에서 종료를 완료했다는 FIN 을 받지 못하고 기다리고 있는 상태이다. 이상태는 양방의 두번의 통신이 이루어졌기 때문에 네트워크의 문제는 아닌 것으로 판단되며,(FIN 을 보내는 순간에 순단이 있어 못받은 것일 수도 있다.) 서버측에서 CLOSE를 처리하지 못하는 경우일 수도 있다. FIN_WAIT_2 역시 일정시간 후 Time Out이 되면 스스로 Closed 하게 된다.



어떠한 이유에서 FIN_WAIT_1과 FIN_WAIT_2 상태인 연결이 많이 남아있다면, 문제가 발생할 수 있다. 물론 일정 시간이 지나 Time Out이 되면 연결이 자동으로 종료되긴 하지만, 이 Time Out이 길어서 많은 수의 소켓이 늘어만 난다면, 메모리 부족으로 더 이상 소켓을 오픈하지 못하는 경우가 발생한다.

(이 경우는 네트워크나 방화벽 또는 어플리케이션에서 close() 처리 등에 대한 문제등으로 발생할 수 있으며, 원인을 찾기가 쉽지 않다.) 

*이러한 문제 해결을 위해서 FIN_WAIT_1과 FIN_WAIT_2 의 Time Out 시간을 적절히 조절할 필요가 있다.*







## TCP의 혼잡제어

흐름제어와 혼잡제어로 인해 호스트간 신뢰성 있는 데이터 전달이 가능하다. 흐름제어는 송신자가 수신자의 버퍼에 맞게 전송속도를 조절하는 것이고, 혼잡제어는 송신자가 네트워크의 상황에 맞춰 흐름을 조절하는 것이다. 빠른 LAN에서 느린 WAN으로 전송하거나 다수의 전송이 하나의 라우터에 몰릴 경우 혼잡이 발생하는데 이러한 문제를 해결하기 위해 TCP에서 혼잡제어 메커니즘을 제공한다. (윈도우 사이즈를 조절하여 혼잡을 제어함)





* **slow-start** 

처음 연결이 될때 수신자의 버퍼에 맞춰 보내게 되면 중간 라우터의 버퍼에 부하가 올 수 있다. 때문에 새로운 패킷을 망에 전송하는 비율과 수신자의 ACK를 관찰하여 윈도우 사이즈를 지수함수적으로 증가시키는 방법이다. 



* **congestion-avoidance(혼잡회피)**

*slow-start*로 지수함수적으로 윈도우 사이즈를 늘리다가 *threshold에* 도달하면 선형적으로 늘리는 방법이다.



* **fast-retransmission (빠른 재전송)**

윈도우 사이즈만큼 패킷을 보내고 받게 되는데 수신자측에서는 마지막으로 받은 패킷의 다음번 순번을 ACK의 Sequance에 담아서 보내게 된다. 만약 중간에 순번이 틀리게 된다면 같은 ACK가 계속 발생하게 되고 송신자 측에서 문제를 감지하고 해당 패킷을 재전송하게 된다. 이러한 문제는 혼잡상황에서 발생하기 때문에 window size를 감소시킨다.



* **fast-recovery (빠른 회복)**

혼잡상태가 발생했을때 window size를 1로 줄이지 않고 반으로 줄이고 선형 증가시키는 방법이다.



![congestionControl](/images/network/congestionControl.png)



중복 ACK가 발생하면 *fast-retransmission*을 수행하고 timeout이 발생하면 *slow-start*를 수행한다.

​    

​    

​    

> 출처

[https://www.hackmageddon.com/2011/04/17/tcp-split-handshake-attack-explained/](https://www.hackmageddon.com/2011/04/17/tcp-split-handshake-attack-explained/)

[http://hyeonstorage.tistory.com/287](http://hyeonstorage.tistory.com/287)

[http://needjarvis.tistory.com/157](http://needjarvis.tistory.com/157)

