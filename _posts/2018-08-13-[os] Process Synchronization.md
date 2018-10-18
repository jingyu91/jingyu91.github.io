---
title: "Process Synchronization"
categories: os
excerpt: "Process Synchronization"
---

## race condition

공유자원에 대해서 여러 프로세스가 동시에 접근하는 상황을 말한다.

사용자 프로그램 혹은 커널에서 공유 메모리에 있는 자원을 사용하다가 시스템콜 혹은 인터럽트가 발생하여 
다른 작업을 실행하는데 이전에 사용하던 자원을 사용하게 되면 일관성을 해치게 된다.



예를들어 공유데이터 x가 있는데 프로세스 A는 `x = x + 1;`  명령을 수행하고, 프로세스 B는 `x = x -1` 명령을 수행한다.

사실 컴퓨터에서는 저 명령어 한줄을 원자적으로 한번에 처리할 수 없다.

실제로는

```c
load x, reg1
inc	reg1
store x, reg1
```

이런식으로 처리를 하게 되는데 A의 명령어 수행중  B의 인터럽트로 x를 변경하는 경우 일관성이 깨지는 것이다.



## Critical Section Problem

위 예제처럼 공유데이터를 접근하는 부분을 **critical section**이라고 한다.

```c
~~~
x = x + 1; // critical section
~~~
```



<u>critical section</u>을 피하는 방법을 소프트웨어적으로 해결하기 위해선 다음과 같은 원칙을 만족시켜야 한다.

1. Mutual Exclusion (상호배제) : 동시에 진입할 수 없다.
2. Progress (진행) : 크리티컬 섹션에 아무도 없을때 원하면 프로세스는 진입할 수 있어야한다.
3. Bounded Waiting (유한대기) : 기다리는 시간이 유한해야 한다. (starvation을 막아야 한다) 



**Alogridm1** 1번 동시에 들어가는 경우를 해결

```c
int turn = 0 // init turn
// process P0의 코드
do {
    while(turn != 0); // 내턴이 아니면 waiting...
	critical section
	turn = 1;
} while(1)

// process P1의 코드
do {
    while(turn != 1); // 내턴이 아니면 waiting...
	critical section
	turn = 0;
} while(1)
```

이 경우 <u>critical secion</u>에는 동시에 진입하는 경우는 해결하지만 한 프로세스가 <u>critical section</u>에 진입할 필요가 없어서 `turn`을 바꿔놓지 않는다면 다른 프로세스는 영원히 못들어 가고 while만 뺑뺑이 돌게된다...



**Alogridm2** algo1의 문제를 해결

```c
// process P0의 코드
do {
    flag[0] = true; // 내 깃발을 듬
    while(flag[1]); // waiting...
    // critical section
    flag[0] = false; // 내 깃발 내림
} while(1)
    
// process P1의 코드
do {
    flag[1] = true; // 내 깃발을 듬
    while(flag[0]); // waiting...
    // critical section
    flag[1] = false; // 내 깃발 내림
} while(1)
```

<u>critical secion</u>에 진입하기 전에 깃발을 들고 작업 후 깃발을 내린다. 그러면 다른 프로세스는 상대의 깃발이 들려있을 경우 while을 돌며 대기하다가 깃발이 내려갈경우 진입하게 된다. 그러나 `flag[0] = true; // 내 깃발을 듬` 에서 인터럽트가 발생해서 다른 프로세스가 실행 될 경우 두 프로세스가 깃발을 동시에 들게 되어 아무도 진입하지 못하는 문제가 발생한다. 결국 동시성은 해결하지만 서로 눈치만 보다 진행이 되지 않는 경우가 생길 수 있다.



**Alogridm3** algo1과 2의 문제 해결 (Peterson의 알고리즘)

```c
// process P0의 코드
do {
    flag[0] = true; // 내 깃발을 듬
    turn = 1; // 상대에게 턴을 넘김
    while(flag[1] && turn == 1); // waiting...
	critical section
    flag[0] = false;
} while(1)
    
// process P1의 코드
do {
    flag[1] = true; // 내 깃발을 듬
    turn = 0; // 상대에게 턴을 넘김
    while(flag[0] && turn == 0); // waiting...
	critical section
    flag[1] = false;
} while(1)
```

turn이라는 변수를 두어서 2와 같이 서로 눈치만 보는 경우를 해결하였다.

깃발을 동시에 들더라도 turn은 0아니면 1의 값을 가지게 되므로 두 프로세스 중 하나만 <u>critical section</u>에 진입하게 된다.

여기서 이 두 코드의 순서를 바꾸게 되면 제대로 동작하지 않으니 유의하자.

```c
flag[1] = true; // 내 깃발을 듬
turn = 0; // 상대에게 턴을 넘김
```

간단해 보이지만 생각보다 잘 짜여진 코드인것 같다.



**그러나** *Peterson의 알고리즘*으로 제대로 동작하는 것은 보장하게 되지만 비효율적인 문제가 발생한다.

왜냐하면 <u>critical section</u>에 진입한 프로세스가 있을경우 다른 프로세스는 cpu를 획득해봤자 while문만 계속 돌면서 cpu를 낭비하기 때문이다. 이를 **Spin Lock**이라고 한다. 	**spin lock : 계속 cpu와 메모리를 쓰면서 wait (busy-waiting)* 



사실 Critical Section 문제를 해결하기 위해 하드웨어 지원을 받는다면 더 쉽게 해결할 수 있다.

하드웨어적으로 **Test & Modify**를 <u>atomic</u>하게 수행하게 된다면 간단히 해결된다.

즉, 앞의 알고리즘처럼 flag와 turn을 셋팅하고 while에서 검사하여 진행하는 것이 아니라 읽고 쓰는 작업을 Test_and_Set() 이라는 하드웨어 지원 함수로 한번에 수행하면 된다는 것이다. 이렇게 <u>atomic</u>하게 수행된다면 어느 순간에 인터럽트 되더라도 일관성을 유지할 수 있다.

```c
boolean lock = false
do {
    while(Test_and_Set(lock)); // 락을 걸면서 바로 critical section에 진입함
    // ciritical section
    lock = false;
}
```

하지만 3번 bounded waiting 문제를 해결하지 못한다. 이 문제를 해결하려면 critical section 후 기다리는 프로세스를 검사해야하고 추가적인 배열이 필요한데 실질적으로 하드웨어적으로 지원하기 힘들다고 한다.



## Semaphores



세마포어는 앞서 살펴본 방식들을 추상화 시킨것이다.

```c
Semaphore S;
P(S); // while(S<=0); S--;
V(S); // S++;
```

P 연산은 critical section에 진입할때 S를 감소시키고 V 연산은 자원을 다시 내놓는 작업을 한다.

세마포어는 자원을 여러 프로세스가 점유할 수 있는 Counting semaphore와 Binary semaphore가 있다.

Binary semaphore는 세마포어가 하나인 경우(0 or 1만 가질 수 있는 세마포어)를 말하며 앞에서 본 Mutual Exclusion의 줄여 **mutex**라고도 한다.



세마포어를 사용하여 critical section문제를 해결하는 코드는 다음과 같다.

```c
// process P1의 코드
Semaphore mutex;

do {
    P(mutex);
	critical section
    V(mutex);
} while(1)
```



이번에는 세마포어를 사용하여 SpinLock문제를 해결해보자.

앞의 세마포어 S는 단순히 자원의 갯수를 의미하지만 세마포어 구조체를 확장하여 Process 리스트를 추가하고 critical section에 진입하지 못하는 프로세스들을 줄세우게 한다.

```c
Typedef struct {
int value;
struct process *L;
} semaphore;
```

이러한 방식은 **block&wake up** 방식이라고 하면 *block()*과 *wakeup()* 을 지원한다.

*block()*은 해당 프로세스의 PCB를 세마포어에 대한 List에 추가한다.

*wakeup()*은 block된 프로세스의 PCB를 레디큐로 옮긴다.

```c
P(S);
/* 
S.value--;
if(S.value < 0) {
add to S.L;
block();
}
*/

V(S);
/*
S.value++;
remove P from S.L;
wakeup(P);
*/
```

프로세스의 상태도에 나와있는 공유메모리의 큐에 잠자게 하는 것이다.

![processState](/images/os/processState.png)

일반적으로 **busy-waiting** 방식보다 **block&wake-up** 방식이 더 효율적이라고 할 수 있다.



세마포어를 사용할때 DeadLock과 Starvation문제가 발생할 가능성이 있다.

**DeadLock** : 둘 이상의 프로세스가 서로 상대방이 가진 자원을 기다리며 block된 상태.

예를들어 P0이 S를 획득하고, P1이 Q를 획득한 상황에서 P0이 Q를 기다리고 P1이 S를 기다린다면 영원히 서로 기다리게 된다.



**세마포어의 단점**

- 코딩하기 힘들다 -> P연산과 V연산을 쌍으로 정확히 코딩해야한다
- 정확성 입증이 힘들다
- 자발적 협력이 필요하다
- 한번의 실수가 전체 시스템에 치명적이다



나중에 모니터를 사용해서 단점을 개선할 수 있다.



## 동기화에 관련된 문제들

* **Bounded Buffer Problem (Producer-Consumer Problem)**

유한한 버퍼를 두고 생산자와 소비자 사이에서 발생할 수 있는 문제이다.

생산자 프로세스는 버퍼가 비어있어야 데이터를 생성할 수 있기 때문에 버퍼에 빈 공간이 있을때까지 대기한다.

소비자 프로세스는 버퍼가 차 있어야 데이터를 읽을 수 있기 때문에 버퍼에 데이터가 있을때까지 대기한다.

당연히 공유메모리에 여러 프로세스가 접근하기 때문에 동기화가 이루어져야하고, busy-waiting 도 해결해야 한다.



세마포어를 사용하여 해결하기 위해 두개의 카운팅 세마포어와 뮤텍스가 필요하다.

`semaphore full = 0, empty = n, mutex = 1;`

```c
// Producer
do {
    produce an item in x // 자원을 생산
    P(empty); // 빈 버퍼를 얻는다 (n--)
    P(mutex); // 다른 생산자나 소비자의 접근을 막는 lock
    add x to buffer 
    V(mutex);
    V(full); // 내용이 들어있는 버퍼를 하나 추가
} while(1);

// Consumer
do {
    P(full); // 내용이 들어있는 버퍼를 얻는다.
    P(mutex);
    remove x from buffer // 자원을 소비
    V(mutex);
    V(empty); // 빈 버퍼를 하나 추가
    consume the item x // 자원을 소비
} while(1);
```



* **Readers Writers Problem**

현실세계에서 DB의 데이터 접근/쓰기 문제와 비슷하다. 한 프로세스가 write중일때 다른 프로세스는 접근할 수 없지만 Read는 동시에 해도 상관없다. 즉, Reader들에 대해서는 동시접근을 허용하며, Writer들에게는 배타적 접근을 지원한다.

```c
Database DB;
int readcount = 0; // 현재 DB에 접근하는 Reader의 수

Semaphore mutex = 1; // readcount의 상호배타적 접근을 위한 mutex
Semaphore db = 1;

// Writer
P(db);
writing DB
V(db);

// Reader
P(mutex); // readcount 통제
readcount++;
if (readcount == 1){
    P(db); // Writer의 접근을 막는 lock (만약 1이 아니라 1보다 크다면 이전에 이미 lock이 걸려있음)
}
V(mutex);

reading...

P(mutex); // readcount 통제
readcount--;
if (readcount == 0) {
    V(db); // Writer의 접근을 허용
}
V(mutex);
```

하지만 Reader가 끊임없이 read를 시도하면 Writer는 Starvation(bounded waiting)이 발생할 수 있다.

때문에 일정시간을 기준으로만 Reader를 받고 Writer에게 기회를 주는 방식으로 해결할 수 있다.



* **Dining Philosophers Problem (식사하는 철학자 문제)**

다섯명의 철학자가 원탁에 앉아 식사하는 상황에서 발생하는 문제이다.

![philosophers](/images/os/philosophers.png)

다섯명의 철학자가 동시에 왼쪽 젓가락을 잡는 경우 DeadLock이 발생한다.

해결 방법으로는 젓가락을 집기 전에 양쪽이 가능한지 먼저 확인한 뒤 밥을 먹게 하는 방법이 있다.

```c
enum { thinking, hungry, eating } state[5];
semaphore self[5] = 0;
semaphore mutex = 1;

void pickup(int i) {
    P(mutex);
    state[i] = hungry;
    test(i); // 양옆이 eating상태가 아닌지 체크하고 내 상태를 eating으로 만든다
    V(mutex);
    P(self[i]);
}
    
void putdown(int i) {
    P(mutex);
    state[i] = thinking;
    // 양옆을 깨워준다
    test((i+4) % 5);
	test((i+1) % 5);
    V(mutex);
}
    
void test(int i) {
    if ( state[(i+4)%5] != eating && state[i] == hungry && state[(i+1)%5] != eating ) {
    	state[i] = eating;
		V(self[i]);
    }
}

// 각각의 Philosopher 
do {
	pickup(i);
	eat();
	putdown(i);
	think();
} while(1)
```



## Monitor

공유 데이터에 대한 안전한 공유를 보장하기 위해 고급언어에서 지원하는 도구



이전의 세마포어에의 단점을 개선하고자 나온것이 모니터이다.

모니터는 공유데이터와 함께 접근하기 위한 operations를 제공한다. 즉, 공유데이터를 접근하기 위해 프로그래머가 직접 lock을 걸고 풀고 할 필요없이 제공하는 operation을 사용함으로써 그 책임을 모니터에게 이양시킬 수 있다.



![monitor](/images/os/monitor.png)

모니터에는 condition변수를 두어 세마포어에서 사용한 block&wake-up 방식으로 구현한 큐처럼 관리한다.

Condition variable은 큐에 줄을 세우는 wait()와 깨우는 signal() 연산을 제공한다.



동기화에 관한 문제를 모니터를 사용해 해결해보자.

* **Bounded Buffer Problem (Producer-Consumer Problem)**

```c
monitor bounded_buffer
{
	int buffer[N]; // 공유데이터가 모니터 안에 정의
	condition full, empty;
	
    void produce(int x) 
    {
    	if (empty is no) { // 버퍼에 남은 공간이 없으면
    		empty.wait(); // 기다린다. 나중에 소비자가 signal()로 빈버퍼가 생김을 알리면 깨어남
    	}
    	add x to empty buffer // 데이터 추가
    	full.signal();
    }
    
    void consume(int* x) 
    {
	    if (full is no) { // 버퍼가 가득 차면
    		full.wait();
    	}
    	remove item and store to x
    	empty.signal();
    }
}
```

세마포어처럼 P연산 V연산을 쓰지 않고 일반적인 상식에 맞게 생산자는 버퍼에 빈공간 있는지 체크하고 없으면 wait()를 한다.

따라서 프로그래머 입장에서는 자연스럽게 코딩할 수 있기 때문에 실수가 적어지게 된다.



- **Dining Philosophers Problem (식사하는 철학자 문제)**

```c
monitor dining_philosopher
{
	enum { thinking, hungry, eating } state[5];
	condition self[5];
	
    void pickup(int i) {
    	state[i] = hungry;
    	test(i); // 양옆이 eating상태가 아닌지 체크하고 내 상태를 eating으로 만든다
        if (state[i] != eating) { // test후에 불가능하면 잠재운다
        	self[i].wait();
        }
    }
    
    void putdown(int i) {
    	state[i] = thinking;
    	// 양옆을 깨워준다
    	test((i+4) % 5);
		test((i+1) % 5);
    }
    
    void test(int i) {
    	if ( state[(i+4)%5] != eating && 
    		 state[i] == hungry && 
    		 state[(i+1)%5] != eating ) {
    		state[i] = eating;
    		self[i].signal();
    	}
    }
}

// 각각의 Philosopher 
{
	pickup(i);
	eat();
	putdown(i);
	think();
} while(1)
```



## DeadLock

둘 이상의 프로세스가 서로 상대방이 가진 자원을 기다리며 block된 상태.



**DeadLock 발생 4가지 조건**

1. **Mutual exclusion (상호 배제)** : 매순간 하나의 프로세스만 자원을 사용할 수 있음
2. **No preemption (비선점)** : 자원을 강제로 빼앗기지 않음
3. **Hold and wait (보유 대기)** : 다른 자원을 기다릴때 보유자원을 내놓지 않음
4. **Circular wait (환형 대기)** : 자원을 기다리는 프로세스간 사이클이 형성되어야 함



다음과 같은 네가지 조건이 모두 성립할 경우에 DeadLock이 발생한다. 다시말하면 네가지중 하나만 제거를 할 경우 해결 가능하다.

1번을 제거할 경우 동기화가 제대로 동작하지 않기 때문에 적절하지 않다.

2번을 제거하려면 선점으로 만들어주는 것인데 이는 프린터가 출력되는 도중 여러 내용이 섞이는 것과 같기 때문에 적절치 않다.

3번을 만족하지 않는다는 것은 모든 자원이 충족될 경우에만 일을 하기 때문에 starvation이 발생할 수 있고 비효율적이다.

4번을 제거하는 것이 가장 적절한 방법으로 식사하는 철학자 문제와 같이 해결할 수 있다.



**Banker's algoridm :** 공유데이터에 복수의 자원이 있을경우 deadlock을 해결하는 방법

unsafe한 상황이 처하지 않도록 유지한다.

현재 할당된 자원과 최대요청자원, 가능한 자원, 필요한 자원을 표로 나타내어 가용자원이 추가요청자원을 충족할 수 있을경우만 자원을 주어서 safe한 상태를 유지한다. 각각의 프로세스들의 최대요청 자원을 사전에 알고있어야 한다.

