# 13. Process Synchronization 4 
#### Process Synchronization(프로세스 동기화), Concurrency Control(병행제어)
  
<br/>

## Classical Problems of Synchronization
1. Bounded-Buffer Problem (Producer-Consumer Problem)
2. Readers and Writers Problem
3. Dining-Philosophers Problem

## Monitor
- 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct
- 세마포어에 비해 프로그래머의 불안을 확연히 줄여주는 병행 제어 방법
- active한 프로세스 하나만이 공유 데이터에 접근할 수 있도록 제어해주는 방법

![image](https://github.com/jooh9992/CodingTest/assets/54580802/2ffa63ef-9df8-4e92-b06c-15a236b9b2ff)

- lock을 걸 필요가 없음 -> monitor에 있는 프로시저들이 차례대로 공유 데이터에 접근하고, 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능하기 때문
- 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없음
- Condition Variable은 wait와 signal 연산에 의해서만 접근 가능
- `x.wait()`
  - x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend 됨
- `x.signal()`
  - x.signal()은 정확하게 하나의 suspend된 프로세스를 resume함
  - suspend된 프로세스가 없으면 아무 일도 일어나지 않음

<br/>

## Monitor를 통해 개선한 Synchronization Problem

```java
monitor bounded_buffer
{
    int buffer[N];
    // 조건 변수는 값을 가지지 않고, 자신의 큐에 프로세스를 매달아서
    // sleep 시키거나 큐에서 프로세스를 깨우는 역할만 수행함.
    condition full, empty;

    void produce(int x) {
        if (empty 버퍼가 없음) {
            empty.wait();
        }
        empty 버퍼에 x를 추가
        full.signal();
    }

    void consume (int *x) {
        if (full 버퍼가 없음) {
            full.wait();
        }
        full 버퍼에서 데이터를 꺼내 간 뒤 *x에 값을 저장
        empty.signal();
    }
}
```
- 생산자

  - empty 버퍼가 없으면 empty 버퍼를 기다리는 큐에서 대기 (wait 연산)
  - empty 버퍼가 있다면 empty 버퍼에 데이터를 집어넣음
  - 작업이 끝나면 full.signal() 를 통해 full 버퍼를 기다리는 큐에게 프로세스 하나를 깨우라고 알림
 
 <br/>

- 소비자

  - full 버퍼가 없으면 full 버퍼를 기다리는 큐에서 대기 (wait 연산)
  - full 버퍼가 있다면 full 버퍼의 데이터를 꺼내서 *x에 값을 저장
  - 작업이 끝나면 empty.signal() 를 통해 empty 버퍼를 기다리는 큐에게 프로세스 하나를 깨우라고 알림

<br/>

## 식사하는 철학자 문제를 모니터로 구현 (Dining Philosophers Example)

```java
monitor dining_philosopher
{
    enum {thinking, hungry, eating} state[5];
    condition self[5];

    void pickup(int i) {
        state[i] = hungry;
        test(i);
        if (state[i] != eating) {
            self[i].wait();
        }
    }

    void putdown(int i) {
        state[i] = thinking;
        test[(i + 4) % 5];
        test[(i + 1) % 5];
    }

    void test(int i) {
        if ((state[(i + 4) % 5] != eating) && (state[i] == hungry) && (state[(i + 1) % 5] != eating)) {
            state[i] = eating;
            self[i].signal();
        }
    }
    void init() {
        for(int i=0; i<5; i++)
            state[i] = thinking;
    }
}
// 위의 코드까지는 monitor 안에 있음
// 각 철학자의 동작
Each Philosopher:
{
    pickup(i);
    eat();
    putdown(i);
    think();
} while (1)
```

- pickup

  - 자신의 상태를 배고픈 상태로 변경
자신이 왼쪽 젓가락과 오른쪽 젓가락을 모두 들 수 있는지 확인
모두 들 수 있다면 자신의 상태를 먹는 상태로 변경하고, wait 큐에서 자신을 깨움
모두 들 수 없다면 wait 큐에 대기
 

- putdown

  - 식사를 마쳤으므로 자신의 상태를 생각 상태로 변경
자신의 왼쪽과 오른쪽 철학자에게 식사할 수 있는 기회를 줌
여기서 기회라는 것은 무조건 식사할 수 있다는 의미는 아니고, test() 함수를 통해 식사가 가능한지 판단할 수 있다는 의미
 

- test

  - 자신이 왼쪽 젓가락과 오른쪽 젓가락을 모두 들 수 있는지 확인하고, 가능하면 자신의 상태를 먹는 상태로 변경하고, wait 큐에서 자신을 깨움