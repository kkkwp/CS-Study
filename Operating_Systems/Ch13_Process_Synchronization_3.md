# Process Synchronization 3

### Classical Problems of Synchronization

- Bounded-Buffer Problem (Producer-Consumer Problem)
- Readers and Writers Problem
- Dining-Philosophers Problem

![aaa](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/945d0ee6-1cc1-4199-8d3e-700a13b7946e)

### Bounded-Buffer Problem

- 생산자의 역할
    - Empty 버퍼 유무 확인 (=없으면 기다린다)
    - 공유데이터에 lock을 건다
    - Empty Buffer에 데이터 입력 및 buffer 조작
    - lock을 푼다
    - Full buffer 하나 증가
- 소비자 역할
    - full 버퍼 유뮤 확인 (=없으면 기다림)
    - 공유데이터에 lock을 건다
    - Full buffer에서 데이터 꺼내고 buffer 조작
    - Lock을 푼다
    - empty buffer 하나 증가

- Shared data
    - buffer 자체 및 buffer 조작 변수(empty/full bufer의 시작 위치)

- Synchronization variables
    - mutual exclusion → Need binary semaphore (shared data의 mutual exclusion을 위해)
        - binary semaphore → 0과 1의 값만 가지며, 상호 배제(mutual exclusion)를 위해 신호 전달 메커니즘을 사용해서 잠금을 구현
    - resource count → Need integer semaphore (남은 full/empty buffer의 수 표시)
        - integer semaphore(=Counting Semaphore) → pool에 있는 resource의 수와 같은 값으로 초기화 되는 세마포어 동기화 기법

### Readers-Writers Problem

![Untitled 1](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/45d5f821-c3df-4591-b47b-a8e0658e0e91)

- 한 process가 DB에 write 중일 때 다른 Process가 접근하면 안됨
- read는 동시에 여럿이 해도 됨
- solution
    - Writer가 DB에 접근 허가를 얻지 못한 상태에서는 모든 대기중인 Reader들을 다 DB에 접근하게 해준다
    - Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용된다
    - 일단 Writer가 DB에 접근 중이면 Reader들은 접근이 금지된다
    - Writer가 DB에 빠져나가야만 Reader의 접근이 허용된다

- Shared data
    - DB 자체
    - readcount → 현재 DB에 접근 중인 Reader의 수

- Synchronization variables
    - binary semaphore 2개 이용
    - mutex → 공유 변수 readcount를 접근하는 코드(critical section)의 mutual exclusion 보장을 위해 사용
    - db → Reader와 writer가 공유 DB 자체를 올바르게 접근하게 하는 역할
    - Starvation 발생 가능
        
        > **여기서 Starvation이란?**
        > 
        > 
        > Writer보다 Reader가 먼저 처리가 되는데, Reader의 처리를 기다리다가 Writer가 처리가 계속 미뤄지고 결국에 굶어죽게 되는 현상
        > 
    

### Dining-Philosophers Problem

- 철학자 다섯이 원형 식탁에 둘러앉아 사고 과정을 가지고, 배고플 땐 밥을 먹는다. 철학자 양쪽엔 각각 젓가락 한 짝씩 놓여있으며, 밥을 먹으려할 땐 다음 과정이 진행된다.
    - 왼쪽 젓가락 집기 → 다른 철학자가 왼쪽 젓가락을 사용하고 있다면 그 사람이 놓을 때까지 대기
    - 왼쪽을 들었다면 오른쪽 들기가 가능. 들 수 없으면 들 수 있을 때까지 생각하며 대기
    - 두 젓가락을 모두 들었다면 일정 시간동안 식사 가능
    - 식사를 마쳤다면 오른쪽 → 왼쪽 순으로 젓가락을 내려놓음
    - 다시 생각하다가 배고프면 왼쪽 젓가락 집기 반복
- 문제점
    - Deadlock의 문제가 발생
        
        > DeadLock 이란?
        > 
        > 
        > 두 개 이상의 작업이 서로 상대방의 작업이 끝나기만을 기다리고 있기 때문에 결과적으로 아무것도 완료되지 못하는 상태
        > 
    - 모든 철학자가 동시에 배가 고파져 왼쪽 젓가락을 집어버린 경우

- 해결 방안
    - 4명의 철학자만이 테이블에 동시에 앉을 수 있도록 한다
    - 젓가락을 두 개 모두 잡을 수 있을 때에만 젓가락을 집을 수 있게 한다
    - 비대칭
        - 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락부터 집도록

### Monitor

- Semaphore의 문제점
    - 코딩하기 힘들다
    - 정확성(correctness)의 입증이 어렵다
    - 자발적 협력(voluntary cooperation)이 필요하다
    - 한번의 실수가 모든 시스템에 치명적 영향

![Untitled](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/ad535ddf-8e13-4056-922a-cdef652657aa)

- 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct
    - 프로세스가 공유 데이터에 접근하기 위해서는 모니터 내부의 프로시저를 통해서만 공유 데이터를 접근할 수 있도록 설계함
    - 공유 데이터들이 있다면 밖에서 아무나 접근할 수 있는 것이 아니라 모니터 내부에 공유 데이터에 접근하는 프로시저를 정의해놓고 이 프로시저를 통해서만 접근하게 제어함
    - 모니터가 내부에 있는 프로시저는 동시에 여러 개가 실행되지 않도록 통제하는 권한을 준다. → 모니터 내에서는 한 번에 하나의 프로세스만이 활동이 가능하도록 제어하므로 프로그래머 입장에서 lock을 임의로 걸 필요가 없다는 장점이 생김
    - 모니터 안에 active한 프로세스가 없을 때, 외부 entry queue에서 기다리고 있는 프로세스가 들어와서 코드를 실행할 수 있게 됨