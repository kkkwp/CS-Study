# 🚦 Deadlocks

## The Deadlock Problem

`Deadlock`이란 일련의 프로세스들이 서로가 가진 자원을 기다리며 block 된 상태(잠들어 있는 상태)를 말한다.

`Resource(자원)`는 HW, SW 등을 포함하는 개념이다. 예를 들면 I/O device, CPU cycle, memory space semaphore 등을 말한다. 또한 프로세스가 자원을 사용하는 절차(Request, Allocate, Use, Release)를 의미하기도 한다. 

<br>

## Deadlock 발생의 4가지조건

아래 4가지 조건을 모두 만족해야 deadlock이 발생할 수 있다. 

1. `Mutual exclution 상호 배제`
    
    매 순간 하나의 프로세스만이 자원을 사용할 수 있다.
    
2. `No preemption 비선점`
    
    프로세스는 자원을 스스로 내어놓을 뿐, 강제로 빼앗기지 않는다.
    
3. `Hold and Wait 보유대기`
    
    자원을 가진 프로세스가 다른 자원을 기다릴 때, 보유 자원을 놓지 않고 계속 가지고 있다.
    
4. `Circular wait 순환대기`
    
    자원을 기다리는 프로세스 간에 사이클이 형성되어야 한다.
    
    ex) 프로세스 $P_0, P_1 … P_n$이 있을 때, $P_0$은 $P_1$이 가진 자원을 기다리고 $P_1$은 $P_2$이 가진 자원을 기다리고 $P_{n-1}$은 $P_n$이 가진 자원을 기다리고… $P_n$은 $P_0$이 가진 자원을 기다리는 상황
    
<br>

## Resourse-Allocation Graph(자원할당그래프)

![스크린샷 2023-05-25 01 35 31](https://github.com/kkkwp/CS-Study/assets/67499154/dda71ba8-a44b-4d5b-ac6a-942f1b63ba85)

자원할당그래프란 deadlock이 발생했는지 알아볼 수 있는 그래프이다.

그림을 설명하자면 원은 process, 사각형은 resource, 사각형 안의 점은 instance, 즉 자원의 수를 의미한다. 

화살표도 방향에 따라 2가지로 나눌 수 있는데, request edge P → R은 이 프로세스가 이 자원을 요청했고 아직 획득은 못한 상태를 말한다. assignment edge R → P는 이 자원이 이 프로세스에 속해 있음, 즉 이 프로세스가 이 자원을 사용 중임을 나타낸다.

이 그림을 보고 deadlock이 발생했는지는 다음을 확인하여 알 수 있다.

1. 그래프에 cycle이 없는 경우? deadlock이 아니다.
2. 그래프에 cycle이 있는 경우?
- 자원 당 하나의 인스턴스만 있다면, deadlock이다.
- 자원 당 여러 개의 인스턴스가 있다면, deadlock일 수도 있고 아닐 수도 있다!

<br>

## Deadlock의 처리 방법

1. `Deadlock Prevention 방지`
    
    자원 할당 시 deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 것을 말한다.
    
2. `Deadlock Avoidance 회피`
    
    자원 요청에 대한 부가적인 정보를 이용해서 deadlock의 가능성이 없는 경우에만 자원을 할당한다. 즉 시스템 state가 원래 state로 돌아올 수 있는 경우에만 자원을 할당하는 것이다.
    
3. `Deadlock Detection and recovery 검출 및 복구`
    
    deadlock 발생은 허용하되, 그에 대한 detection 루틴을 두어 deadlock 발견 시 recover 한다.
    즉, 시스템이 느려졌거나 할 때 혹시 deadlock이 있는 건 아닐까.. 하고 상황에 대한 detection을 한 후에 deadlock이 있다면 recover하는 방법이다.
    
4. `Deadlock Ignorance 무시`
    
    deadlock을 시스템이 책임지지 않는다. 사람이 프로세스를 kill해서 직접 해결해야 한다. 
    UNIX를 포함한 대부분의 OS가 채택하고 있다. 왜냐하면 deadlock은 빈번히 발생하는 이벤트가 아니기 때문에, 이를 미연에 방지하기 위해서 훨씬 더 많은 오버헤드를 발생시키는 것은 비효율적이기 때문이다.
    
<br>

## Deadlock Prevention

자원 할당시 deadlock의 4가지 필요 조건 중 어느 하나가 만족되지 않도록 하는 방법이다.

1. `Mutual Exclution`
    
    이 조건은 막을 수 없는 조건이다. 공유해서는 안 되는 자원이라는 조건은 반드시 성립해야 한다.
    
2. `No Preemption`
    
    자원을 빼앗아올 수 있게 하면 된다! CPU와 memory의 경우 자원의 state를 쉽게 save하고 restore 할 수 있기 때문에 이를 활용한다.
    
3. `Hold and Wait`
    
    내가 가진 자원을 내놓지 않으면서 다른 것을 요청할 때 발생한다. 따라서 프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않도록 해준다.
    
    - 프로세스 시작 시 모든 필요한 자원을 할당 받게 하는 방법
    프로세스 종료 시 모두 반납하고, wait할 일이 없다! 대신 당장 쓰지 않는 자원까지 모두 가지고 있으므로 비효율적이다.
    - 자원이 필요할 경우 보유 자원을 모두 놓고 다시 요청하는 방법
    이미 hold 중인데 다른 자원을 요청해야 한다면 이미 hold 중인 자원을 모두 놓고 요청한다. 즉 아무 것도 가지고 있지 않은 상태에서 요청하는 것이다.
    
4. `Circular Wait`
    
     1, 3, 4번 자원이 필요한 경우를 살펴보면, 1번 가지고 있으면서 3번을 요청하고, 3번을 가지고 있으면서 1번을 요청하니까 deadlock이 발생한다. 따라서 1번 먼저 획득해야만 3번을 획득할 수 있게 하자. 즉 모든 자원 유형에 할당 순서를 정하여 정해진 순서대로만 자원을 할당하면 된다. 예를 들어, 순서가 3인 자원 $R_i$을 보유중인 프로세스가 순서가 1인 자원 $R_j$을 할당받기 위해서는 우선 $R_i$를 release 해야 한다.
    

→ Deadlock Preventoin은 자원에 대한 이용률(utilization) 저하, 전체 시스템의 성능(throughput) 감소, starvation 문제가 발생할 수 있다. 즉, 나오지 않을 수도 있는 deadlock을 예방하려고 이렇게 하면 너무 비효율적이다!

<br>

## Deadlock Avoidance

자원 요청에 대한 부가적인 정보(어떤 자원을, 몇 개까지 사용할 예정)를 이용해서 자원 할당이 deadlock으로부터 safe한지를 동적으로 조사해서 안전한 경우에만 자원을 할당하는 방법이다. 

가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법이다. 즉, 앞으로 프로세스가 시작될 때 이 프로세스가 평생동안 쓸 자원의 최대량을 미리 알고 있다고 가정한다.

Deadlock Avoidance 알고리즘에는 2가지가 있다. 

자원당 인스턴스가 1개인 경우 `Resource Allocation Graph algorithm`을 사용하고, 

자원당 인스턴스가 여러 개인 경우 `Banker’s Algorithm`을 사용한다.

> 💡 Safe State vs. Unsafe State
>
> ![스크린샷 2023-05-25 02 14 05](https://github.com/kkkwp/CS-Study/assets/67499154/db9ecb34-e83a-4eaa-a178-70ef9bd74755)
>
> - 시스템이 safe state에 있는 경우 → deadlock이 아니다.
> - 시스템이 unsafe state에 있는 경우 → deadlock일 수도 있고, 아닐 수도 있다.
>
> deadlock avoidance은 시스템이 unsafe state에 들어가지 않는 것을 보장한다.

<br>

## Resource Allocation Graph algorithm

![스크린샷 2023-05-25 02 20 45](https://github.com/kkkwp/CS-Study/assets/67499154/53891710-31b3-49b8-8562-4176ed1b636b)

그림을 설명하자면, 앞서 설명한 자원할당그래프에 Claim edge(점선 화살표)가 추가되었다. 이 점선 화살표는 프로세스 P가 자원 R을 미래에 요청할 수 있음을 뜻한다. 즉 이 프로세스가 평생에 한번은 이 자원을 사용할 일이 있음을 의미한다. 이 프로세스가 해당 자원을 요청하면 request edge(실선)로 바뀌고, 자원 R이 release 되면 assignment edge는 다시 claim edge(점선)로 바뀐다. 

점선을 포함하여 cycle이 생기지 않는 경우에만 요청 자원을 할당하는 알고리즘이다.

이 알고리즘에 의하면 cycle 생성 여부 조사 시 프로세스의 개수가 n일 때 $O(n^2)$ 시간이 걸린다.

<br>

## Banker’s Algorithm

![스크린샷 2023-05-25 02 26 09](https://github.com/kkkwp/CS-Study/assets/67499154/fae4e32b-7474-4b39-8f6d-8f491f70abe9)

2가지 가정을 전제로 한다.

1. 모든 프로세스는 자원의 최대 사용량을 미리 명시한다. 
2. 프로세스가 요청 자원을 모두 할당받은 경우, 유한 시간 안에 이들 자원을 다시 반납한다.

그림의 용어를 설명하자면 다음과 같다.

- `allocation` : 현재 각 프로세스에 할당된 인스턴스
- `max` : 프로세스가 시작될 때 평생 사용할 최대 자원 사용량
- `available` : 전체 인스턴스 - 현재 할당된 자원(allocation)
- `need` : 추가 요청 가능량(max - allocation)

기본적으로 자원 요청시 safe 상태를 유지할 경우에만 자원을 할당한다. 따라서 총 요청 자원의 수가 가용 자원의 수보다 적은 프로세스를 선택한다. 그런 프로세스가 없으면 unsafe 상태라는 의미이고, 그런 프로세스가 있으면 그 프로세스에게 자원을 할당하게 된다. 할당 받은 프로세스가 종료되면 모든 자원을 반납한다. 모든 프로세스가 종료될 때가지 이러한 과정을 반복하게 된다.

즉 need가 available을 가지고 모두 충족이 가능한지, 즉 시스템 내의 프로세스에 대한safe sequence가 존재하는지 보면 된다.

- 가능하다면? 할당해준다.
- 가능하지 않다면? 할당하지 않고 기다린다. 다른 프로세스들이 일을 끝마치고 자원을 반환하면, 이때의 available과 비교하여 다시 할당할지 판단한다.

> 💡 safe sequence
>
> - 프로세스의 sequence < $P_1, P_2 … P_n$ >이 safe 하려면, $P_i(1 \le i \le n)$의 자원 요청이 “가용자원 + 모든 $P_j (j \lt i)$ 의 보유 자원” 에 의해 충족되어야 한다.
> - 조건을 만족하면 다음 방법으로 모든 프로세스의 수행을 보장한다.
>     - $P_i$의 자원 요청이 즉시 충족될 수 없으면 모든 $P_j(j \lt i)$가 종료될 때까지 기다린다.
>     - $P_{i-1}$이 종료되면 $P_i$의 자원요청을 만족시켜 수행한다.
