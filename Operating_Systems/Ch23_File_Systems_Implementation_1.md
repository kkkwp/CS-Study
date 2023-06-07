# File Systems Implementation 1

## **Allocation of File Data in Disk**

- 파일은 크기가 균일하지 않다는 특징이 있음.
그래서 파일을 동일한 크기 단위로 sector로 나누어 저장, 디스크에 파일을 저장하는 방법은 크게 3가지가 존재하는데, 메모리 관리 기법 중 페이징 기법과 유사

  - **Contiguous Allocation (연속 할당)**
  - **Linked Allocation (연결 할당)**
  - **Indexed Allocation (인덱스를 이용한 할당)**

### **1. Contiguous Allocation (연속 할당)**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/54158100-c49a-4b19-96d9-1368c9b30749)


- 하나의 파일이 디스크 상에 연속해서 저장되는 방식으로, 나누어진 각 블록들이 연속된 번호를 부여 받아 저장
- 장점
    - 빠른 입출력
        - 대부분의 접근 시간은 헤더가 움직이면서 읽어 들이는 시간
        - 한 번의 seek/rotation으로 많은 바이트 transfer (모두 연속해서 붙어 있으므로 추가적인 탐색 비용이 소요되지 않음)
        - 따라서 Realtime 파일 또는 이미 run 중이던 프로세스의 swapping 용도로 사용
    - 임의 접근(Direct access, random access)이 가능
- 단점
    - 외부 단편화가 발생
    - 파일 grow가 어려움
        - 파일의 크기는 유동적임 이때 파일의 크기가 커질 때 제약 사항이 발생
        - 파일 생성 시 얼마나 큰 hole을 배당할 것인가?
        - grow 정도를 크게 할수록 내부 단편화 문제가 커짐
    

### **2. Linked Allocation (연결 할당)**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/dad2c5dc-43eb-4365-9f1a-47d76f7e7ca8)

- 빈 자리가 있으면 아무 곳이나 할당함
- 블로의 마지막 부분에 다음 블록이 어디에 위치해 있는지 저장해 두는 것
- 파일의 시작 위치만을 디렉토리가 가지고 있게 되는 것

- 장점
    - 외부 단편화가 발생하지 않음
- 단점
    - 첫번째 위치만 저장해 두기 때문에 순차 접근만 가능하게 됨
    - 신뢰성 문제
        - 한 sector가 고장나 pointer가 유실되면 그 이후 모든 sector로 접근할 수 없음
    - 포인터를 위한 공간이 필요하므로 공간 효율성을 떨어뜨리게 됨
- 변형
    - File-allocation table (FAT) 파일 시스템
        - 포인터를 별도의 위치에 보관하여 신뢰성 문제와 공간 효율성 문제를 해결

### **3. Indexed Allocation (인덱스를 이용한 할당)**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/1057ccd8-c503-40a5-9427-ec193963ccb2)

- 파일이 어디에 나눠져 있는지 인덱스를 적어 두는 블록 하나를 활용하고, 이를 인덱스 블록이라 부름

- 장점
    - 외부 단편화가 발생하지 않음
    - 임의 접근이 가능
- 단점
    - 작은 파일의 경우 공간 낭비가 심하며, 실제로 많은 파일들이 사이즈가 작음
    - 매우 큰 파일의 경우 하나의 인덱스 블록으로 커버할 수 없음
        - 해결 방안: linked scheme, multi-level index
        - 전자는 index block을 여러 개 두는 것이고, 후자는 블록의 마지막에 다음 index 블록을 가리키는 값을 설정하여 서로 연결하는 것

---
<br/>

## **UNIX 파일 시스템의 구조**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/e3f67a6e-3ded-47ac-b354-adb1fc0e8b2d)

- 유닉스 파일 시스템의 중요 개념
    1. Boot block
        - 부팅에 필요한 정보를 담고 있는 블록
        - 모든 파일 시스템에 존재하는 블록
    2. Super block
        - 파일 시스템에 관한 총체적인 정보를 담고 있는 블록
        - 어느 부분이 비어 있는 블록인지, 어느 부분이 사용 중인 블록인지, 어디부터가 Inode 블록인지 Data 블록인지 등을 알려 주는 정보를 가지고 있음
    3. Inode list
        - 파일 이름을 제외한 파일의 모든 메타 데이터를 따로 저장
        - 파일 하나 당 Inode가 하나씩 할당이 되고, 해당 Inode는 그 파일의 메타 데이터를 갖고 있다. 이때 파일의 이름은 디렉토리가 가지고 있는데, 디렉토리는 파일의 이름과 Inode 번호를 저장 (이전에 디렉토리가 파일의 메타 데이터를 가지고 있다고 했는데, 유닉스 파일 시스템의 디렉토리는 파일의 이름 및 파일의 대부분의 메타 데이터를 저장하는 Inode의 번호를 가지고 있음)
        - direct blocks는 파일이 존재하는 인덱스를 저장하는 인덱스 블록, 파일의 크기가 크지 않다면 이 블록을 이용하여 파일을 접근할 수 있음
        - direct blocks으로 커버할 수 있는 크기보다 저장 용량이 큰 파일은 single indirect를 통해서 하나의 level을 두어서 저장하는 방식을 취하고, 그보다 더 큰 파일은 double indirect, 더 큰 파일은 triple indirect 방식을 취함
    4. Data block
        - 파일의 실제 내용을 보관하는 블록
        - 이 중 디렉토리 파일은 자신의 디렉토리에 속한 파일의 이름과 Inode 번호를 가지고 있음
        - 나머지 정보들은 inode에 저장되어 있지만 file name은 directory에 저장됨

<br/>

## **FAT 파일 시스템**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/badd148f-8e93-4d98-bf39-bc80297f1d50)

- FAT 파일 시스템은 윈도우즈 계열에서 주로 사용
- 파일의 메타 데이터의 일부(위치 정보)를 FAT에 저장하고, 나머지 정보는 디렉토리가 가지고 있음 (파일 이름, 접근 권한, 소유주, 파일의 첫 번째 위치 등)
- 위 사진에서 217번이 첫 번째 블록인데, 다음 블록의 위치를 FAT에 별도로 관리한다. FAT 테이블 전체를 메모리에 올려 놓았으므로 연결 할당의 단점(임의 접근 불가, 신뢰성 문제, 공간 효율성 문제)을 해결 가능, 참고로 FAT는 중요한 정보이므로 복제본을 만들어 두어야 함

<br/>

## **빈 공간 관리**

![image](https://github.com/jooh9992/CodingTest/assets/54580802/77d532cb-3d24-4baf-b9d2-4a906cda52dc)

- sector가 할당되고 나서 발생하는 hole을 어떻게 관리할 것인가?
1. Linked list
    - 모든 free 블록을 링크로 연결 (free list)
    - 연속적인 가용 공간을 찾기 어렵
    - 공간의 낭비가 없음
2. Grouping
    - Linked list의 변형
    - 첫 번째 free 블록이 n 개의 포인터를 가짐
        - 하나의 free 블록에 나머지 free 블록에 대한 위치 정보를 저장하는 방식
3. Counting
    - 프로그램들이 종종 여러 개의 연속적인 블록을 할당하고 반납한다는 성질에 착안

<br/>

## **디렉토리 구현**

- 선형 리스트
    - <file name, file의 메타 데이터>의 리스트
    - 구현이 간단
    - 디렉토리 내에 파일이 있는지 찾기 위해서는 선형 탐색이 필요 (O(N))
- 해시 테이블
    - 선형 리스트 + 해싱
    - 해시 테이블은 file name을 이 파일의 선형 리스트의 위치로 바꾸어줌
    - 탐색 시간이 O(1)
    - 해시 충돌이 발생할 수 있음

![image](https://github.com/jooh9992/CodingTest/assets/54580802/c4958937-ddfa-489a-b9b0-5018d3abe354)

- 파일의 메타 데이터의 보관 위치
    - 디렉토리 내에 직접 보관
    - 디렉토리에는 포인터를 두고 다른 곳에 보관
        - Inode, FAT 등
- Long file name의 지원
    - <file name, file의 메타 데이터>의 리스트에서 각 엔트리는 일반적으로 고정 크기
    - 하지만 file name이 고정 크기의 엔트리 길이보다 길어지는 경우 엔트리의 마지막 부분에 file name의 뒷 부분이 위치한 곳의 포인터를 둠
    - file 이름의 나머지 부분은 동일한 directory file의 일부에 존재

![image](https://github.com/jooh9992/CodingTest/assets/54580802/323c88ba-dcd5-4741-8e50-f32b5af8a611)

<br/>

## **VFS와 NFS**

- Virtual File System (VFS)
    - 서로 다른 다양한 파일 시스템에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해 주는 OS의 레이어
- Network File System (NFS)
    - 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
    - NFS는 분산 환경에서 대표적인 파일 공유 방법

![image](https://github.com/jooh9992/CodingTest/assets/54580802/9d35cc8e-ee87-42ca-afb6-9543867505b6)

- 어떤 파일 시스템을 쓰든 상관 없이 VFS 인터페이스를 사용
- 분산 시스템에서는 네트워크를 통해 파일을 공유하기 위해 NFS 클라이언트와 NFS 서버가 이용됨