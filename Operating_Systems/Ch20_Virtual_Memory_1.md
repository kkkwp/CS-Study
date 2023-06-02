# Virtual Memory 1
물리적인 메모리의 주소 변환은 운영체제가 관여하지 않는다. 그에 반에 Virtual Memory 기법은 전적으로 운영체제가 관리하고 있다.

Virtual Memory(가상 메모리)라는 것은 프로세스 전체가 메모리 내에 올라오지 않더라도 실행이 가능하도록 하는 기법이다. 가상 메모리는 물리 메모리로부터 논리 메모리를 분리해서 메인 메모리를 균일한 크기의 저장 공간으로 구성된 배열로 추상화 시켜준다. 이 기법을 통해 프로그래머는 메모리의 크기 제약으로부터 자유로워지고, 공유 메모리 구현도 가능하게 해주며, 프로세스 생성을 효율적으로 처리할 수 있는 기법도 제공한다.

그러나 이러한 가상 메모리는 구현하기 어렵고, 만약 잘못 사용하게 된다면 성능이 현저히 저하될 수 있는 부작용이 있다.

## Demand Paging

말 그대로 요청(demand)이 있으면 그 페이징을 메모리에 올리겠다는 것이다.

즉, 실제로 필요할 때 page를 메모리에 올리는 것이다.

이를 통해서 CPU 이용률과 처리율이 높아지고, 더 많은 사용자를 수용할 수 있게 된다.

Demand paging은 page table에서 해당 page가 메모리에 있는지를 나타내는 valid-invalid bit를 사용한다.
bit가 invalid인 경우 페이지가 물리적 메모리에 없다는 것이다.

따라서 처음에는 모든 page entry가 invalid로 초기화되어 있고, 주소 변환 시 bit가 invalid로 되어 있다면 page fault라는 에러가 발생한다.

- address traslation 시에 invalid bit가 set되어 있으면 “page fault” → CPU는 자동으로 운영체제에게 넘어감(page fault trap이 걸린다고 표현)

## Page Fault

위키피디아에는 아래와 같이 정의하고 있다.
> 컴퓨팅에서 페이지 오류 ( PF 또는 하드 오류 라고도 함 ).
> 프로세스가 적절한 준비 없이 메모리 페이지 에 액세스할 때 메모리 관리 장치 (MMU) 에서 발생하는 예외 입니다 .

Invalid page를 접근하면, MMU(Memory Management Unit: 메모리 관리 장치)가 trap을 발생시키게 된다.

Kernel mod로 들어가서 page fault handler가 invoke된다.

> MMU(Memory Management Unit)는 컴퓨터 시스템의 하드웨어 구성 요소 중 하나로, 주로 운영체제가 프로세스들이 사용하는 가상 메모리 주소를 실제 물리 메모리 주소로 매핑해 주는 역할을 한다.

메모리 관리 장치(MMU)는 주로 CPU와 함께 동작하며, 가상 주소를 물리 주소로 변환하는 데 필요한 정보를 관리하고, 매핑 오류를 감지하고 처리하는 기능을 수행한다.
> 
> 
> "Invalid page"는 유효하지 않은 메모리 페이지를 참조하려고 할 때 발생하는 상황을 의미한다. 예를 들어, 프로세스가 할당받지 않은 메모리 영역에 접근하려고 하거나, 이미 해제된 메모리에 접근하려고 할 때 등이 이에 해당할 수 있다. 이런 경우, MMU는 이러한 오류를 감지하고 운영체제에 알리기 위해 trap을 발생시킨다.
> 
> Trap은 예외 상황이나 중요한 이벤트가 발생했을 때 운영체제에게 제어를 넘기는 것을 의미한다. 이 경우, trap을 발생시킨 MMU는 운영체제의 커널 모드로 전환되어, page fault handler(페이지 부재 처리기)라고 불리는 특별한 코드를 실행하게 된다.
> 
> page fault handler는 MMU가 감지한 오류의 종류에 따라 적절한 조치를 취하게 되는데 예를 들어, 유효하지 않은 페이지에 접근한 경우 해당 프로세스에 대한 오류 처리 루틴을 실행하거나, 필요한 페이지를 디스크로부터 메모리로 로드하는 등의 작업을 수행할 수 있게 된다.
> 

### Page Fault의 일반적인 처리 순서

---

**[1] Invalid reference? (eg. bad address, protection violation) ⇒ abort process**

: 페이지 부재가 발생한 원인이 유효하지 않은 주소나 보호 규칙 위반 등의 잘못된 참조인 경우, 해당 프로세스를 중단(abort)시킨다. 즉, 프로세스 실행을 중단하고 종료시키는 것을 의미한다.

**[2] Get an empty page frame. (없으면 뺏어온다: replace)**

: 빈 페이지 프레임(물리 메모리에서 사용 가능한 공간)을 얻는다. 만약 빈 페이지 프레임이 없다면, 페이지 교체(replacement) 알고리즘에 따라 기존에 할당된 페이지를 대체하여 사용한다.

**[3] 해당 페이지를 disk에서 memory로 읽어온다.**

1. disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함(block)
2. Disk read가 끝나면 page tables entry 기록, valid/invalid bit = “valid”
3. ready queue에 process를 insert → dispatch later

: 페이지 부재로 인해 디스크에 저장되어 있는 해당 페이지를 메모리로 읽어온다. 이 과정에서 프로세스는 디스크 I/O가 완료될 때까지 CPU를 양보하고(preempt) 대기하게 된다. 읽기 작업이 완료되면, 페이지 테이블 항목을 갱신하고(valid/invalid bit를 “valid”로 설정) 준비된 프로세스를 대기열(ready queue)에 삽입한 후 나중에 실행(dispatch)될 수 있도록 한다.

**[4] 이 프로세스가 CPU를 잡고 다시 running**

: 페이지 부재 처리가 완료되고, 프로세스가 CPU를 다시 점유하게 된다. 따라서 중단되었던 프로세스의 실행을 재개할 수 있다.

**[5] 아까 중단되었던 instruction을 재개**

: 페이지 부재로 인해 중단되었던 명령어 실행을 재개한다. 페이지 부재 처리 이후에는 원래 실행되어야 했던 명령어가 이어서 실행될 수 있게 된다.

---

![image](https://github.com/IToriginal/CS-Study/assets/117193889/b28d976a-3ab4-4dcd-a8cd-1923b83edb21)

1️⃣ 여기 1번을 보면 메모리 reference 가 있었는데, 주소 변환을 하려고 봤더니 invalid 표시가 되어 있었다. 그러면 이 Page가 메모리에 올라와 있지 않다는 의미라서 

2️⃣ trap이 걸려 CPU가 자동으로 운영체제에게 넘어가게 된다. 

3️⃣ 운영체제는 backing store에 있는 페이지를 

4️⃣ physical memory(물리적인 메모리)로 올려놓는다. 만약에 물리적인 메모리에 빈 프레임이 없으면, 무언가를 쫓아내서라도 물리적인 메모리에 올려놔야 한다. 올려놓는 작업이 다 끝나게 되면, 

5️⃣ 해당하는 프레임 번호를 page table의 엔트리에다 적어놓고, invalid였던 것을 valid로 바꾸게 된다. 

6️⃣ 그런 다음에 CPU를 다시 얻어서 주소변환을 하게 되면, valid로 되어있고, 주소 변환을 정상적으로 해서 원하는 물리적인 메모리의 프레임에 접근이 가능해지는 것이다.

## Performance of Demand Paging

![image](https://github.com/IToriginal/CS-Study/assets/117193889/b67613c2-0f6e-4605-ab08-7b1d7fa95b07)

Page fault가 발생했을 때, disk로 접근하는 것은 매우 오래 걸리는 작업이다.

Page fault가 얼마나 크게 발생 했느냐에 따라서 메모리 접근 시간이 크게 달라지는 것이다.

### if,

- page fault의 비율이 0이다?
그러면 절때 page fault가 발생하지 않고 메모리에서 모두 참조가 되는 것을 의미한다.
- page fault의 비율이 1이다?
매번 메모리 참조할 때 마다 page fault가 발생한다는 것을 의미한다.

대부분의 경우는 page fault가 발생하지 않고, 메모리로 부터 직접 주소변환을 할 수 있다. 

그 외에는 page fault가 발생하여 disk 접근을 해야 하는 것이다.

### Effective Access Time

Page default가 발생하지 않은 경우, 메모리 접근 시간만 걸리게 될 것이다.

Page default가 발생하는 경우, 운영체제로 CPU가 넘어가서 하드웨어적으로 Page fault를 처리하는 Overhead 등 문제 시간을 더해 시간이 크게 걸리게 된다.

---

## Free Frame이 없는 경우

### Page Replacement

Page Fault 처리 단계에서 “물리적인 메모리에 빈 프레임이 없으면, 무언가를 쫓아내서라도 물리적인 메모리에 올려놔야 한다.” 라고 언급했다. 여기서 쫓아내야한다는 것을 Page Replacement 라고 부른다.

OS가 하는 업무이며 어떤 frame을 빼앗아올지 결정해야하는데 곧바로 사용되지 않을 page를 쫓아내는 것이 좋다.

이러한 알고리즘을 Replcement Algorithm이라 한다.

### Replcement Algorithm

- 위의 page fault의 비율이 0에 가까울 수 있도록 해주는 것이 목표다.

### Reference string

EX: 1, 2, 3, 4, 1, 2, 5, 1, 2, 3, 4, 5

시간 순서에 따라 Page들을 서로 다른 번호로 부여해두고 Page들이 참조된 순서를 나열해 놓은 것이다.

여기서 동일한 순서가 뒤에 다시 나오는 것은 Replacement Algorithm으로 인해 쫓겨났다가 다시 참조되는 경우일 수 있다.

---

![image](https://github.com/IToriginal/CS-Study/assets/117193889/a60ad505-2194-4a45-b3f7-2e3c6f2d40cd)

1️⃣ physical memory에서 희생자(victim)가 선정되면, 디스크로 쫓아버린다.(만약에 희생자의 내용이 쫓겨나기 전에 Write 등의 이유로 변경이 되었다면, backing store에 변경 내용을 써줘야한다.)

2️⃣ 쫓겨난 Page에 대한 bit는 invalid로 바꿔준다.

3️⃣ 새로운 Page를 메모리에 올린다.

4️⃣ 해당 새로운 Page에 대한 bit를 valid로 변경한다.

이러한 역할을 운영체제가 하게 되는 것이다.

---

그렇다면 어떠한 알고리즘이 가장 좋은 알고리즘일까?

## Optimal Algorithm

Page fault를 가장 적게 한다.

시스템에서는 미래를 알수 없음. 하지만 Optimal Algorithm은 미래에 참조되는 Reference string을 다 알고 있다고 가정을 하는 것이다. 때문에 실제 시스템에서는 사용될 수 없다. 그래서 이 알고리즘을 Offline optimal Algorithm이라고 한다.

![image](https://github.com/IToriginal/CS-Study/assets/117193889/31ab37ff-54a1-4b0f-b6bf-f70b9e08cfca)

가장 먼 미래에 참조되는 page를 쫓아낸다. 

- 1번 부터 4번까지 메모리에 올라오게 될때 메모리가 없기 때문에 page fault가 발생하게 되고
- 1번이 다시 올라올때는 이미 메모리에 올라와 있기 때문에 메모리에서 직접 참고가 된다.
(빨간 ⇒ page fault, 연보라색 ⇒ 메모리에서 직접 참조가 되는 경우)
- 5번을 넣게 될때 이 알고리즘은 미래를 알기 때문에 다음에 올 1, 2, 3을 쫓아내지 않는다. 그래서 미래에서 가장 먼 4를 미리 알고 있기 때문에 4를 쫓아내고 5를 올리게 되는 것이다.
- 아무리 좋은 알고리즘을 만들어도 이보다 성능이 좋을 수 없다.
- 그렇기 때문에 이 알고리즘은 다른 알고리즘에 대한 참고로 사용하게 된다.

---

## 미래를 모르는 상태에서의 알고리즘 - 과거를 참조

### FIFO(First In First Out) Algorithm

![image](https://github.com/IToriginal/CS-Study/assets/117193889/18c00d68-5479-40db-8adb-19b664570767)

- 가장 간단한 페이지 교체 알고리즘
- 어떤 페이지를 교체해야 할 때, 메모리에 올라온 지 가장 오래된 페이지를 쫓아낸다.
- 이 알고리즘은 나쁜 성질을 가지고 있다. 메모리 frame 수를 늘렸는데 오히려 page fault 수가 늘어나는 현상(FIFO Anomaly)이 발생하는 것이다.

### LRU(Least Recentrly Used) Algorithm

![image](https://github.com/IToriginal/CS-Study/assets/117193889/86addaeb-343b-4fca-bf53-17d5f1ce0e76)

- 가장 오랜 기간 동안 사용되지 않은 페이지를 교체하는 것이다.
- 페이지마다 마지막 사용 시간을 유지

### LFU(Least Frequently Used) Algorithm

![image](https://github.com/IToriginal/CS-Study/assets/117193889/cbd6b57e-cfd8-427c-8073-9356f8096919)

- 참조 횟수가 가장 적은 페이지를 교체하는 것
- 과거에 참조가 많이된 것은 미래에 참조될 확률이 높다라는 성질을 가지고 있다.