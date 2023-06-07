# Disk Management & Scheduling 1

## Disk Structure

- logical block
    - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
    - 주소를 가진 1차원 배열처럼 취급
    - 정보를 전송하는 최소 단위
- Sector
    - Logical block이 물리적인 디스크에 매핑된 위치
    - Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다.

## Disk Management

- physical formatting (Low-level formatting)
    - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    - 각 섹터는 header + 실제 data(보통 512bytes) + trailer로 구성
    - header와 trailer는 sector number, ECC (Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영
- Partitioning
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
    - OS는 이것을 독립적 disk로 취급 (logical disk)
- Logical formatting
    - 파일 시스템을 만드는 것
    - FAT, inode, free space 등의 구조 포함
- Booting
    - ROM에 있는 “small bootstrap loader”의 실행
    - sector 0 (boot block)을 load 하여 실행
    - sector 0은 “full Bootstrap loader program”
    - OS를 디스크에서 load하여 실행

## Disk Scheduling

![Untitled](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/ae54621f-a09e-46ae-8aad-a35c96ff532a)

- Access time의 구성
    - Seek time
        - 헤드의 해당 실린더로 움직이는데 걸리는 시간
        - 대부분 디스크를 읽는 시간은 Seek time 이다.
    - Rotational Latency
        - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
    - Transfer time
        - 실제 데이터의 전송 시간
- Disk bandwidth
    - 단위 시간 당 전송된 바이트의 수
- Disk Scheduling
    - seek time을 최소화하는 것이 목표
    - Seek time === seek distance
    - 요청이 들어온 순서대로 처리하면 비효율적이게 된다. 그렇기 때문에 디스크 스케줄링 알고리즘이 나온 것이다.

## Disk Sceduling Algorithm

### FCFS (First Come First Service)

![Untitled 1](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/24fd09aa-b70a-41e8-9e6e-af5c6023c1b0)

- 들어온 순서대로 순차적으로 처리하는 방식

### SSTF (Shortest Seek Tme First)

![Untitled 2](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/d8b617ac-55ac-4bd7-8658-f420f8b86571)

- header의 기준으로 가장 가까운 섹터로 옮겨가는 방식이다.
- 가장 큰 문제는 만약 queue에 초기 header로 부터 먼 데이터가 들어왔다면 그 데이터는 처리가 될 때까지 오래걸릴 수 있기에, starvation 문제가 야기될 수 있다.

### SCAN

![Untitled 3](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/d69cf2d7-846d-426d-832d-7e167a8378b7)

- 가장 안쪽 → 바깥쪽 → 안쪽 → 바깥쪽 → … 으로 움직이기 때문에 효율적인 알고리즘 기법이다.
- 허나 대기시간에 편차가 일어난다.
    - 가운데는 반 바퀴, 가장 자리는 한 바퀴처럼 disk arm이 돌아오는 시간이 데이터가 위치한 곳마다 대기 시간이 달라질 수 있다.

### C-SCAN

![Untitled 4](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/32cf6eb9-8f66-4de1-beca-f979e1a86843)

- SCAN보다 균일하게 대기 시간을 줄 수 있다.

### Other Algorithms

- N-SCAN
    - SCAN의 변형 알고리즘
    - 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service
    - 디스크 암이 움직이면서 들어온 데이터들을 잠시 keep 해놓았다가 추후에 처리하겠다고 예정 시켜놓는다.
- LOOK and C-LOOK
    
    ![Untitled 5](https://github.com/byeongJoo05/algorithmStudy/assets/84984586/3a430023-fe69-4096-9b7c-a4576fd43ed1)
    
    - SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동
    - LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동한다.