# Process 2

## 동기식/비동기식 입출력 부연 설명
- 동기식 입출력
  - 본인이 직접하는게 아니라 운영체제를 통해서 해야함
  - 사용자 프로세스는 운영체제에 입출력을 요청함, 좀 오래 걸림, 입출력이 끝날때가지 아무일도 안하고 프로세스가 기다리면 동기식

- 비동기식 입출력
  - 일을 시키고 기다리지 않고 CPU가 새로운 instruction를 실행

---

## **Thread**
- 프로세스 내에서 실행 되는 여러 흐름의 단위 혹은 프로세스가 할당 받은 자원을 이용하는 실행 흐름의 단위

![image](https://github.com/jooh9992/CodingTest/assets/54580802/e47d0c6b-58c1-4702-9ed4-cc8249eba6c5)

- Thread의 구성
  - program counter
  - register set
  - starck space
- Thread가 동료 Thread와 공유하는 부분( = task)
  - code section
  - data section
  - OS resources  
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task를 볼 수 있음

## Thread의 장점
- **응답성 (Responsiveness)**
  - 다중 스레드로 구성된 테스크 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 태스크 내의 다른 스레드가 실행(running)되어 빠른 처리를 할 수 있음
- **자원 공유 (Resource Sharing)**
    - 하나의 프로세스 안에 CPU 수행 단위인 쓰레드를 두게 되면 code, data, resource 자원을 공유하여 효율적으로 자원 활용이 가능하다.
- **경제성 (Economy)**
    - 동일한 일을 수행하는 다중 쓰레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
    - 새로운 프로세스 하나를 만드는 것 보다 기존의 프로세스에 쓰레드를 추가하는 것이 오버헤드가 훨씬 적다.
        - 프로세스의 Context Switching과 달리 캐시 메모리를 초기화할 필요가 없고 Code, Data, Resource 영역을 공유하므로 Stack 영역만 처리하면 되기 때문이다.
- **멀티 프로세서 아키텍처에서의 이용성 (Utilization of MP Architectures)**
    - 각각의 쓰레드가 서로 다른 CPU를 가지고 병렬적으로 작업을 진행해서 훨씬 효율적으로 작업을 수행할 수 있다.