# Memory Management 2

## Paging Exmaple

![Untitled](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/1b502892-9008-4999-b825-2b92748b3167)

- 페이징이란 적절한 크기로 잘라서 번호로 매겨서 메모리에 할당하는 것
- logical memory → page table → physical memory 형태로 넘어감
    - 주소 변환을 위해 논리적 메모리는 페이지 단위로 분할, 물리적 메모리는 프레임 단위로 분할 되어 서로 매칭됨
    - 논리적 주소를 물리적 주소로 변환하기 위해 페이지 테이블을 활용함

## Address Translation Architecture

![Untitled 1](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/9fe333f1-f49a-4806-88ca-d1c80464ed28)

위에 대한 설명에 관련된 회로

- 페이징 기법에서는 CPU가 사용되는 논리적 주소를 페이지 번호(p)와 페이지 오프셋(d)로 나누어 주소 변환에 사용함
- 페이지 번호는 각 페이지별 주소 변환 정보를 담고 있는 페이지 테이블 접근시 인덱스로 사용되며 해당 인덱스의 항목에는 그 페이지의 물리적 메모리 상 기준 주소가 저장됨
- 따라서 특정 프로세스의 p번째 페이지가 위치한 물리적 메모리의 시작 위치를 알고 싶다면 해당 프로세스의 페이지 테이블에서 p번째 항목을 찾아보면 됨
- 페이지 오프셋은 하나의 페이지 내에서의 변위를 알려준다. 따라서 기준 주소 값에 변위를 더함으로써 요청된 논리적 주소에 대응하는 물리적 주소를 얻을 수 있음
- 위 그림에서 f (물리적 주소의 시작 위치) + d를 취하면 처음에 CPU가 요청한 논리적 주소에 대응하는 물리적 주소가 됨

## Implementation of Page Table

- Page table은 main memory에 상주
- Page-table base register (PTBR) 가 page table을 가리킴
- Page-table length register (PTLR) 가 테이블 크기를 보관
- 모든 메모리 접근 연산에는 2번의 memory access 필요
- page table 접근 1번, 실제 data/instruction 접근 1번
- 속도 향상을 위해 associative register 혹은 translation look-aside buffer (TLB) 라고 불리는 고속의 lookup hardware cache 사용

## Associative Register

- Associative registers (TLB) : parallel search가 가능
    - TLB에는 page table 중 일부만 존재
- Address translation
    - page table 중 일부가 associative register에 보관되어 있음
    - 만약 해당 page #가 associative register에 있는 경우 곧바로 frame #를 얻음
    - 그렇지 않은 경우 main memory에 있는 page table로부터 frame #를 얻음
    - TLB는 context switch 때 flush (remove old entries)

## Effective Access Time

- Associative register lookup time(=연관 레지스터에 접근하는 시간) = ε
- memory cycle time(=메모리에 접근하는 시간) = 1
- Hit ratio(=요청된 페이지에 대한 주소 변환 정보가 연관 레지스터에 존재할 확률) = α
    - associative register에서 찾아지는 비율
- Effective Access Time(EAT)
    - $EAT = (1+e)α + (2 + ε)(1-α) = 2 + e - α$

## Two-Level Page Table

![Untitled 2](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/ae25365c-3916-4114-9a0e-9603301c5bd7)

- 현대의 컴퓨터는 address space가 매우 큰 프로그램 지원
    - 예를 들어 32비트 주소 체계를 사용하는 컴퓨터에서는 4GB의 주소 공간을 갖는 프로그램을 지원한다.
        - 페이지 사이즈가 4K라면, 한 프로세스당 페이지 테이블을 위해 1M 크기의 페이지 테이블 메모리 공간이 필요하다.
        - 그러나 대부분의 프로그램은 4G의 주소 공간 중 지극히 일부분만 사용하므로 페이지 테이블 공간이 심하게 낭비된다.
        
    - page table 자체를 page로 구성
    - 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 NULL (대응하는 inner page table이 없다.)

## Two-Level Paging Example

- logical address ( 4K page size를 갖는 32 비트 체계 컴퓨터) 의 구성
    - 20 bit 의 page number
    - 12 bit의 page offset
- page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나뉜다 ( 각 page table entry가 4B)
    - 10-bit 의 page number
    - 10-bit 의 page offset
- 따라서, logical address는 다음과 같음
    
    ![Untitled 3](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/9f36b1d7-f337-47a5-bd66-8010e15b6e44)
    
    - P1은 outer page table의 index
    - P2는 outer page table의 page에서의 변위