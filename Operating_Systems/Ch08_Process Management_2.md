# Process Management 2

## `wait()` 시스템 콜

- 프로세스 A가 wait() 시스템 콜을 호출하면
    - 커널은 child가 종료될 때까지 프로세스 A를 sleep시킴 (block 상태)
    - Child process가 종료되면 커널은 프로세스 A를 깨움 (ready 상태)

## `exit()` 시스템 콜

- 프로세스의 종료
    - 자발적 종료
        - 마지막 문장 수행 후 exit() 시스템 콜 실행
        - 프로그램 스스로 종료됨
    
    - 비자발적 종료
        - 부모 프로세스가 자식 프로세스를 강제 종료시킴
        - 사람이 kill, break 등을 사용해서 프로세스 종료시킨 경우
        - 부모가 종료하는 경우
            - 이유 - 자식들이 종료된 후 부모가 종료됨

## 프로세스 간 협력

- 독립적 프로세스
    - 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치치 못함
- 협력 프로세스
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘(**IPC**: Interprocess Communication)
    - 메시지를 전달하는 방법
        - message passing - 커널을 통해 메시지 전달
    - 주소 공간을 공유하는 방법
        - shared memory: 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음
    - thread : 스레드는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능

## Message Passing

- Message system
    - 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템
- Direct Communication
    - 통신하려는 프로세스의 이름을 명시적으로 표시
- Indirect Communication
    - mailbox (또는 port)를 통해 메시지를 간접 전달
        
        > 운영체제에서 mailbox(우체통)는 프로세스 간에 통신을 위해 사용되는 메커니즘입니다. Mailbox는 메시지를 보내고 받는데 사용되며, 다른 프로세스에게 데이터를 전달하거나 동기화를 위해 사용될 수 있습니다.
        > 

## CPU and I/O Bursts in Program Execution

- 프로그램은 CPU 버스트와 I/O 버스트가 순차적으로 작동하며 실행됨
    - CPU가 작업을 하고, 사람이 입출력을 하고, CPU가 작업을 하고….
- 프로그램의 종료에 따라 빈도와 주기가 달라짐

## CPU-burst Time의 분포

- I/O 버스트를 아주 짧게 쓰는 경우 I/O bound job 이라고 부름
- CPU 버스트를 아주 짧게 쓰는 경우 CPU bound job 이라고 부름
- CPU-burst가 필요한 이유는 Interactive job에게 적절한 response 제공을 하기 때문이며, CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용하기 때문이다.

## 프로세스의 특성 분류

- 프로세스는 그 특성에 따라 다음 두 가지로 나눔
    - I/O-bound process
        - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
        - many short CPU bursts
    
    - CPU-boun process
        - 계산 위주의 job
        - few very long CPU bursts

## CPU Scheduler & Dispatcher

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름

- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김
    - 이 과정을 context switch(문맥 교환)라고 함

- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우임
    - Running → Blocked (ex. I/O 요청하는 시스템 콜)
    - Running → Ready (ex. 할당시간만료로 timer interrupt)
    - Blocked → Ready (ex. I/O 완료 후 인터럽트)
    - Terminate
    
    - 1, 4에서의 스케줄링은 nonpreemptive (=강제로 빼앗지 않고 자진 반납)
    - All other scheduling is preemptive (=강제로 빼앗음)