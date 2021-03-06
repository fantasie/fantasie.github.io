---
layout: post
title: 프로세스와 쓰레드의 차이
category: blog
tags: [thread]
---
기존의 프로세스 개념으로 두가지 이상의 일을 동시에 처리하려면 빈번한 컨텍스트 스위칭이 일어나게 되는데, 이렇게 되면 성능 저하가 발생하게 된다. 이를 어느정도 해소하기 위해 컨텍스트 스위칭 시 저장되고 복원되는 내용을 줄여줄 필요가 있었고, 이렇게 해서 나온 것이 쓰레드이다.

프로세스와 쓰레드의 관계는 플랫폼에 따라 크게 달라진다. 플랫폼이 같더라도 JVM의 구현 방법에 따라 프로세스와 쓰레드의 관계는 달라진다. 그러나 일반적으로는 "한 개의 프로세스 안에 복수개의 쓰레드가 구축된다.

<!-- more -->

### 쓰레드는 메모리를 공유한다
프로세스와 쓰레드의 최대 차이점은 메모리 공유의 유무이다. 프로세스는 각각 독립된 메모리 공간을 가진다. 다른 프로세스의 메모리를 맘대로 읽거나 쓸 수 없다. 즉, 어떤 프로세스의 메모리 공간이 다른 프로세스로 인해 망가질 염려가 없다.

반면 쓰레드는 메모리를 공유한다. (Java의 메모리 모델에는 공유메모리와 캐시메모리가 있는데, 여기서 말하는 것은 공유메모리이다.)
한 개의 쓰레드가 메모리 상에 정보를 적고, 그것을 다른 쓰레드에서 읽는 것은 흔한일이다. "메모리를 공유하고 있다"는 것은 "인스턴스를 공유하고 있다"는 뜻이다. Java의 인스턴스는 메모리상에 있고, 복수개의 쓰레드가 그 인스턴스를 읽고 쓸 수 있다.
쓰레드가 메모리를 공유하고 있어 쓰레드 간 통신은 매우 쉽고 자연스럽게 구현할 수 있다. 단, 하나의 인스턴스에 복수개의 쓰레드가 동시에 접근하게 되므로 mutual exclusion을 올바로 실행할 필요가 있다.

### 쓰레드는 context-switching이 가볍다
프로세스와 쓰레드의 또 다른 차이점은 컨텍스트 스위칭의 무게이다. 멀티프로세스 환경에서, 동작중인 프로세스가 전환될 때 프로세스는 자신의 현재 상태(컨텍스트 정보)를 일단 보관한다. 그리고 새롭게 동작을 개시하는 프로세스는 이전에 보관해 두었던 자신의 컨텍스트 정보를 복구한다. 이렇게 정보를 바꾸는 행위, context-switching에는 시간이 걸린다.
동작중인 쓰레드가 전환될 때, 쓰레드는 프로세스와 마찬가지로 컨텍스트 스위칭을 수행한다. 그러나 쓰레드가 관리하고 있는 컨텍스트 정보는 프로세스보다 적다. 즉 쓰레드의 컨텍스트 스위칭은 프로세스의 컨텍스트 스위칭보다 가볍게 이뤄진다.

-----------------

#### 좀 더 자세히 보면, 프로세스의 메모리 구조는 다음과 같다.
 - Code 영역
 - Data 영역
 - Heap 영역
 - Stack 영역

1. 독립적 스택 할당

각 프로세스는 독립적이기 때문에, 서로의 프로세스 영역을 침범할 수 없도록 되어있다. 즉, 코드 영역만 공유한다. 반면 쓰레드는 이 영역들 중 스택 영역을 제외한 부분을 공유한다. 즉 코드, 데이터, 힙 영역을 공유한다. 스택 영역이 제외되는 이유는, '추가적인 실행 흐름'을 만들 수 있다는 의미가 된다. 스택에는 지역변수 및 리턴 주소값 등이 저장되기 때문이다.

2. 코드 영역 공유

프로세스 같은 경우는 완전히 독립된 구조이기 때문에 A 프로세스에서 B 프로세스의 함수를 호출하거나 하는 것이 불가능하다. 그러나 쓰레드는 프로세스에서 공유되는 자원이 있기 때문에, 프로세스에서 쓰레드들을 생성하고 같은 코드를 수행하는 쓰레드가 2개 이상 생성되어도 서로 독자적으로 코드를 진행하게 되는 것이다. 단, 쓰레드마다 독자적인 program counter가 있어야 가능한 것이다.

3. 데이터 영역, 힙 영역 공유

쓰레드는 서로간의 데이터 영역(전역, 정적 변수 및 함수) 및 힙 영역(동적 할당된 부분)을 공유할 수 있다. 따라서 기존의 프로세스간 통신을 위해 IPC라는 기법을 사용했던 번거로움을 없앨 수 있다는 장점이 생겼다.

결국 프로세스는 쓰레드를 담는 그릇에 지나지 않는다. 쓰레드는 최소 1개씩은 존재할 수 밖에 없다. 그 1개는 바로 '메인 쓰레드'이다. 운영체제시간에 배웠던 프로세스의 상태 전이, 그리고 스케쥴링 등은 프로세스 단위이기 보다는 쓰레드 단위로 보는 것이 맞다. 그렇다고 프로세스 단위를 아예 배제해 버리는 것은 아니다. 3개의 쓰레드를 가지고 있는 프로세스 A와 2개의 쓰레드를 가지는 프로세스 B가 있는데, 여기서 프로세스간의 컨텍스트 스위칭은 충분히 일어날 수 있는 것이다. 다만 프로세스 범위 안의 쓰레드간 컨텍스트 스위칭 속도와, 프로세스 범위의 컨텍스트 스위칭 속도는 확실히 차이가 난다는 것이다.

### Thread Safety
결국 멀티쓰레드 환경에서는 쓰레드 안정성 문제가 가장 큰 이슈인데

 - 서로 다른 쓰레드가 데이터와 힙 영역을 공유하기 때문에, 어떤 쓰레드가 다른 쓰레드에서 사용중인 변수나 자료구조를 망가트릴 수 있음.
 - 그렇다고 너무 신중하게 잘못 구현을 하면 데드락 상태에 빠질 수 있음. 동기화할 부분은 작게, 한번에 한개의 객체만 동기화.
 - 주의깊게 코드를 살피는 것이 최선임. 여러 객체가 동일한 리소스 집합을 공유한다면, 리소스 집합을 사용하는 순서가 같아야 함.
 - `어떤 리소스를 공유하는가?`에 초점을 맞추어 동기화를 고려한다.

-------

### 그 밖에 쓰레드 관련 이론

쓰레드에서 caller에게 정보를 넘기는 방법은?

 - start(), run() 메소드는 아무것도 리턴하지 않음.
 - 쓰레드를 상속받은 객체에 getter를 만들고, caller가 start() 실행 후 getter() 실행 -> No~~!!!
 + start()이 끝난지 안끝난지 알 수 없는 와중에 getter()가 실행됨.. -> race condition. 서로 다른 쓰레드의 상대적인 속도에 따라 정확한 답을 얻거나, 그렇지 못하거나 할 수 있으나, 이것을 제어할 수 없음.
 - 더 좋은 방법은 폴링(polling) : caller가 while(getter() != null) 을 체크함. 잘 동작하나, 필요 이상의 일을 하게 됨 (CPU 사이클 낭비)

Better solution은 => "콜백"

 - 폴링처럼 caller가 반복적으로 체크하는 것이 아니라, 각 쓰레드가 caller에게 답을 알려주는 것.
 - run()의 마지막줄에 caller 클래스의 메소드를 호출함(+인자 전달) 그리고 그 메소드에서 결과값을 출력하거나 함.


#### 쓰레드의 상태
쓰레드가 다른 쓰레드를 위해 멈추거나, 멈춘다고 알려주는 경우는 다음과 같다.

 - I/O 때문에 블록될 때
 - 동기화된 객체가 lock 상태라서 블록될 때
 - 양보할 때 (yield)
 - Sleep
 - 다른 쓰레드에 합류할 때 (Thread join)
 - 어떤 객체를 기다릴 때
 - 끝날 때
 - 우선순위가 더 높은 쓰레드가 나타나서 선점할 때
 - 지연될 때 > deprecated
 - 멈출 때 > deprecated

블록
- 쓰레드가 정지한 상태. 다른 리소스를 기다리는 경우 언제라도 일어날 수 있음.
- I/O 블록 : 가장 흔한 경우는 네트워크 프로그램에서 I/O때문에 블록되어서 쓰레드가 자발적으로 CPU를 놓는 경우. CPU는 네트워크나 디스크에 비해 속도가 매우 빠르기 때문에 네트워크 프로그램은 네트워크에서 데이터를 I/O하기 위해 자주 블록됨. 겨우 몇 밀리초동안 블록된다고 해도 이 시간이면 다른 쓰레드가 어떤 일을 처리하기에 충분하다.
- 락 블록  : synchronized 블록이나 메소드를 만났을 때, 다른 쓰레드가 락을 가지고 있다면 풀릴 때 까지 기다릴 수 밖에 없다.
- 블록될 때, 어느 경우라도 쓰레드가 이미 가지고 있던 락을 반환하지는 않는다. I/O블록에서는 별 무리 없으나(I/O가 언젠가 끝나서 쓰레드가 계속 수행되거나 or IOException이 발생하여 락을 반환할 것이기 때문), 락 블록에서는 데드락 발생 확률이 있다.

양보 (yield)
- 쓰레드가 자발적/명시적으로 CPU 제어권을 포기. Thread.yield()를 호출하면 됨.
- 양보를 해도 쓰레드가 가지고 있던 락을 반환하지는 않음. 즉, 양보를 하는 쓰레드는 동기화 되지 않는 쓰레드인 것이 이상적임. (동기화가 요구된다면, 락을 반환하지 않는 이상 CPU를 양보한다고 해도.. 별 의미 없을 수도 있음.)
- 우선순위가 같은 다른 쓰레드에게 기회를 주기 위함.

Sleep
- 양보보다 좀 더 강력한 형태. 다른 쓰레드의 준비 여부와 상관 없이 실행을 멈춰버림. 우선순위가 같은 쓰레드 뿐 아니라 낮은 쓰레드에게도 실행 기회가 동등하게 주어짐.
- Sleep을 해도 쓰레드가 가지고 있던 락을 반환하지는 않음. 즉, 이런 락을 필요로 하는 다른 쓰레드는 CPU Time에 여유가 있다고 해도 블록되는 것임. 따라서 동기화된 메소드나 블록에서는 쓰레드의 sleep을 피해야 함.

Thread Join
- 한 쓰레드가 다른 쓰레드의 결과를 필요로 하는 것은 드문 일이지만..
- 특정 쓰레드가 끝나기를 기다리는 것.  특정쓰레드.join();

객체 기다리기 (wait)
- 쓰레드는 자신이 락을 건 객체를 기다릴 수 있다. wait를 하면 그 객체에 대한 락이 해제되며, 다른 쓰레드의 지시를 기다린다. 다른 쓰레드가 그 객체를 변경한 다음에, notify를 해주면 작업이 계속 된다.
- 다른 쓰레드가 계속 실행되기 위해서 쓰레드를 기다리거나 알려주지 않아도 된다는 점에서 조인과 차이가 있다. wait는 어떤 객체나 리소스가 특정 상태에 도달하기까지 수행을 잠시 중지하는데 사용한다. 반면 Join은 쓰레드가 끝날 때 까지 기다리는 것이다.
- wait 메소드는 Thread 클래스가 아닌 java.lang.Object 클래스의 메소드임.
- 정해진 시간이 지났을 때 / 쓰레드가 인터럽트 되었을 때 / notify 되었을 때 깨어남.

우선순위에 기반한 선점
- 선점형 스케줄링에서, 나보다 우선순위가 높은 쓰레드가 나타났을 때. 만약 우선순위가 높은 쓰레드가 실행을 끝마치거나 블록되는 경우에는 일반적으로 라운드로빈 스케줄링에 따라 "우선순위가 낮아 가장 오랫동안 실행되지 않은 쓰레드"를 실행한다.

끝내기
- 가장 기본적인 끝내기는 run() 메소드의 실행이 끝나는 것.