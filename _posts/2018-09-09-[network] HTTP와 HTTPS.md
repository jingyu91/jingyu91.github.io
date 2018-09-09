---
title: "HTTP와 HTTPS 프로토콜과 인증서"
category: network
---

​    

# Protocol

프로토콜(Protocol)은 컴퓨터나 네트워크 장비가 서로 통신하기 위해 미리 정해 놓은 약속을 뜻한다.





## HTTP

HyperText Transfer Protocol의 약자로 WWW(World Wide Web)에서 hypertext 문서를 교환하기 위하여 사용되는 통신규약이다. 

대표적인 규칙으로는 Status code와 Request Method가 있다.

 

**Status code:**  http 통신중 요청의 상태 정보를 제공한다. 공식 적인 상태코드 목록은 [IANA](http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml)에서 확인할 수 있다.

상태코드의 첫 숫자는 응답의 종류를 나타낸다.

1. 1xx - Informational - 정보교환
2. 2xx - Success - 성공
   200 - OK	- 요청이 성공적으로 전송됨
3. 3xx - Redirection - 방향 지정
   301 - Moved Permanently - 요청 페이지의 영구적인 위치 변화
   302 - Found	- 요청 페이지이 일시적인 위치 변화
4. 4xx - Client Error - 클라이언트 오류
   404 - Not Found - 요청받은 자원을 서버에서 찾을 수 없을때 나타나는 상태 
   405 - Method Not Allowed - 서버에서 사용자가 요청한 주소의 메소드를 지원하지 않을때 나타남
5. 5xx - server Error - 서버 오류

​     



**Request methods:** 요청이 수행될 작업의 방법을 나타낸다.

1. OPTIONS: 요청한 URL에 어떠한 메소드 요청이 가능한지 묻는다.

2. GET: 다른 작업없이 데이터의 검색에 이용.

3. HEAD: 데이터의 검색에 이용하나 GET과는 다르게 응답 HEADER만 받는다.

4. POST: URL에 새로운 데이터를 보낼때 사용.

5. PUT: URL에 저장될 정보를 보낸다.

6. DELETE: URL의 리소스를 삭제한다.

7. TRACE: 보낸 메세지를 다시 돌려받는다.

8. CONNECT: 프록시에서 사용되는 예약 메소드.

    

​    

**HTTP의 한계**

1. 암호화 안된 방식으로 데이터를 주고 받음 : 데이터를 중간에 가로챌 수 있음.
2. 통신하는 상대방의 신원을 확인하지 않음 : 상대방이 누군지 확인하지 않고 요청/응답을 보냄. 중간에 상대방인척하고 데이터를 가로챌 수 있음.

​     



## HTTPS 

HTTP를 SSL(Secure Socket Layer)로 한층 더 감싸서 보안을 강화한 것.  => HTTPS는 SSL프로토콜 위에서  돌아가는 프로토콜이다.

기존 **HTTP의 한계**를 극복한다.

1. 데이터를 암호화하고
2. 통신하는 상대방의 신원을 확인함.



SSL인증은 신뢰할 수 있는 제 3자(CA)의 인증서를 기반으로 돌아간다.

SSL은 **보안**과 **성능**을 고려하여 **대칭키**와 **공개키** 라는 두 가지 인증 방식을 가지고 있다.

​    



### **대칭키**

![symmetricKey](/images/network/symmetricKey.png)

대칭키(symmetric key) 방식은 동일한 키로 암호화와 복호화를 같이 할 수 있는 방식의 암호화 기법을 의미한다. 즉, A를 k라는 key로 암호화하여 B로 만들었을때, B를 k로 복호화 할 수 있다는 뜻이다.

대칭키 방식은 단점이 있다. 암호를 주고 받는 사람들 사이에 대칭키를 전달하는 것이 어렵다는 점이다. 대칭키가 유출되면 키를 획득한 공격자는 암호의 내용을 복호화 할 수 있기 때문에 암호가 무용지물이 되기 때문이다. 이런 배경에서 나온 암호화 방식이 공개키방식이다.

​    



### **공개키**

![publicKey](/images/network/publicKey.png)

공개키(public key) 방식은 한 키로 암호화하면 다른 쌍의 키로 복호화 할 수 있는 private키와 public키의 쌍으로 구성되는 방식이다.

private키는 절대로 공개해서는 안되는 키로 자신만 가지고 있고, public키는 타인에게 공개한다. public키가 공개되어도 암호화된 내용은 private키로만 복호화 가능하기 때문에 안전하다. 사실 공개키 방식에서는 데이터를 보호하는 목적보다 데이터의 신원을 보장하는 목적이 더 크다. public키로 암호화된 데이터는 보호되어 private키를 가진 유일한 대상만 복호화 할 수 있지만, private키로 암호화된 데이터는 누구나 열어볼 수 있기 때문이다. 웹 사이트에서 비밀번호 찾기를 하면 임시 비밀번호를 주고 비밀번호를 변경하라는 문구가 뜨는 것은 단방향 암호화로 원래 비밀번호를 찾지 못하는 이유일 수 도 있겠지만 타인이 읽을 수도 있기 때문일 것이다. 하지만 암호화된 데이터를 변조하는 것은 private키가 유출되지 않는 이상 불가능하기 때문에 데이터의 신원은 확실히 보장받을 수 있다. 데이터를 제공한 사람의 신원을 보장해주는 것을 **전자서명**이라고 부른다.

 



공개키 방식은 2048bit를 사용하여 128bit를 사용하는 대칭키 방식보다 performance차이가 크게 발생한다. 때문에 **SSL에서는 공개키 방식과 대칭키 방식을 조합하여 사용한다.**

클라이언트와 서버는 처음 handshake과정에서 클라이언트의 브라우저에 있는 CA리스트와 서버 인증서의 CA가 매칭되는지 검사하고, 랜덤데이터를 사용하여 session키를 생성한다. 이 과정은 공개키 방식으로 암호화되어 진행되고 그 이후는 session키를 대칭키로 사용하여 실제 데이터를 주고 받게 된다.

​    

​    

​    

> 출처

[http://sejoong.github.io/dev/2015/02/15/dev/](http://sejoong.github.io/dev/2015/02/15/dev/)

[https://opentutorials.org/course/228/4894](https://opentutorials.org/course/228/4894)

[https://www.bsidesoft.com/?p=3340](https://www.bsidesoft.com/?p=3340)

[https://hanjungv.github.io/2017-11-07-1_CS_SSL/](https://hanjungv.github.io/2017-11-07-1_CS_SSL/)