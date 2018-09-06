---
title: "DataLink 계층의 기능"
category: network
---

# DataLink

내 컴퓨터에서 다른 컴퓨터로 요청을 할때 링크로 연결되 무수한 라우터(컴퓨터)를 거쳐 목적지에 도착한다. 컴퓨터와 컴퓨터 사이, 즉 physical - physical 사이 통신은 DataLink를 거치게 된다. 



## DataLink의 중요 역할

### Frame sysnchronization (동기화)

frame의 동기를 맞춘다. 

**frame : 컴퓨터와 컴퓨터 사이 주고 받는 데이터의 단위*



* Flow control

수신측와 송신측의 처리속도가 다르기 때문에 전송량이 처리량보다 크다면 데이터가 유실될 수 있다. 이러한 경우를 방지하기 위해서 송신측의 패킷 전송량을 제어하는 기법이다.



* Error control

통신과정에서 발생하는 error를 처리한다.



* Physical addressing

두 장비 사이의 고유성을 다룬다.



* Access control

누가 데이터를 보내게 할지 정한다.



즉 데이터 링크계층에서는 **누가 데이터를 보낼것인지, 얼마나 보낼것인지, 어떻게 에러를 다룰지를** 다룬다.



## Flow control

흐름 제어를 하는 기법은 세가지가 있다.

* XON / XOFF

시리얼 터미널 사이에서 데이터를 더 이상 받지 못할 경우 on/off 하여 제어한다. 예를 들어 프린터와 컴퓨터 사이에서 프린터큐가  넘치면 off하고 프린터큐에 여유분이 생기면 on하여 데이터를 받는다.

 

* Stop-and-wait

frame을 하나씩 보낸다.

데이터를 보낸 후 ACK가 올때까지 기다린다.



* Sliding window

연속해서 보낼 수 있는 양(window size)을 정해서 frame을 계속해서 보낸다.

window size는 ACK를 받지 않아도 보낼 수 있는 프레임의 갯수를 의미한다. *Stop-and-wait*는 window size가 1인 슬라이딩 위도우 기법이라고 볼 수 있다.

전송측에서는 프레임을 전송할때마다 왼쪽 경계를 오른쪽으로 이동시키고, 전송한 프레임에 대한 ACK를 받을때마다 윈도우의 오른쪽 경계를 오른쪽으로 이동시킨다.

수신측에서는 수신받을때마다 왼쪽 경계를 오른쪽으로 이동시키고, ACK를 전송할때마다 오른쪽 경계를 오른쪽으로 이동시킨다.



##  Error Control

오류 제어기법은 오류 검출과 재전송 기능을 한다.

ARQ(Automatic Repeat Request) : 프레임이 손상될 경우 재전송을 통해 오류를 복수한다.

이전에 살펴본 **Flow control** 에서 *stop-and-wait*는 정지-대기 ARQ를 사용하여 전송한 프레임의 복사본을 유지하여 NAK를 받거나 ACK가 유실되면 timeout 발생으로 재전송한다.

*sliding-window*는 *Go-back-n ARQ*나 *Selective-reject ARQ*를 사용하여 구현한다.

*Go-back-n ARQ*는 손실된 프레임이 발견될 경우 마지막에 ACK를 제대로 받은 프레임 이후로 다시 모든 프레임을 재전송하는 기법이다. 제대로 받은 프레임도 다시 재전송하기 때문에 구현은 단순하지만 비효율적인 문제가 발생한다.

*Selective-reject ARQ*는 이러한 단점을 극복하였지만 별도의 버퍼와 재정렬이 필요하여 구현이 복잡하다.