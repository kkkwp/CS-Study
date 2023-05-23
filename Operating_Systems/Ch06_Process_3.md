# Process 3

## Single thread
- registers, stack은 각각 1개씩 있음

## Multi thread
- registers, stack은 별도로 각각 1개씩 있음

## Thread의 단점
- 자원의 공유로 인한 동기화 문제가 발생할 수 있음
- 디버깅이 까다로움

## 구현 방식
- **Kernel Threads**
    - 운영체제가 여러 개의 쓰레드가 있다는 사실을 알고 있어서 쓰레드 간의 CPU 교환을 커널이 CPU 스케줄링하듯이 관리
    - 예시) Window 95, 98, NT, Solaris, Digital UNIX, Mack
- **User Threads**
    - 운영체제가 여러 개의 쓰레드가 있다는 사실을 모르는 상태에서 사용자 프로그램이 라이브러리의 지원을 받아 스스로 여러 개의 쓰레드를 관리
    - 커널이 쓰레드의 존재를 모르기 때문에 구현에 제약이 있을 수 있음
    - 예시) POSIX Pthreads, Mach C-threads, Solaris threads
- **Real-Time Threads**