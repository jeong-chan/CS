# System Structure & Program Execution(2)
<br><br>
## 동기식 입출력과 비동기식 입출력

![Sync_Async](https://user-images.githubusercontent.com/77624879/162800326-30cde019-09d5-48c8-899a-f1af9598cc98.png)

<br><br>
### 동기식 입출력(synchronous I/O)

- I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
- 구현방법1
    - I/O가 끝날때까지 CPU를 낭비시킴
    - 매시점 하나의 I/O만 일어날 수 있음
- 구현방법2(주로 사용)
    - I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
    - I/O처리를 기다리는 줄에 그 프로그램을 줄 세움
    - 다른 프로그램에게 CPU를 줌
<br><br>
### 비동기식 입출력(Asynchronous I/O)

- I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감

*두 경우 모두 I/O의 완료는 인터럽트로 알려준다.
<br><br>
### DMA(Direct Memory Acess)

- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU의 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
- 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴

![image](https://user-images.githubusercontent.com/77624879/162800359-8df4db66-694e-4d63-b6df-8cd0a78c3db9.png)

<br><br>
### 서로 다른 입출력 명령어

- I/O를 수행하는 special instruction에 의해
- Memory Mapped I/O에 의해

![io](https://user-images.githubusercontent.com/77624879/162800408-941b8495-79e3-4bc1-8df9-6a440d7d85f4.png)

<br><br>
### 저장장치 계층 구조

![992DB8435AEF240E25](https://user-images.githubusercontent.com/77624879/162800435-d5327c08-3db3-433e-9104-baac231e660c.png)

- Primary(Executable) : CPU가 직접 접근 가능, 바이트 단위 (cpu~main memory)
- Secondary : CPU가 직접 접근 불가능, 섹터 단위 (disk~ Tape)
- Caching : 재사용이 목적, 데이터를 더 빠른 저장공간에 복사해 놓는 기법
<br><br>
### 프로그램의 실행(메모리 load)

![2](https://user-images.githubusercontent.com/77624879/162800464-914e592e-18e6-49c3-a6eb-95dfd35a63fc.png)

- Virtual Memory : 각 프로그램마다 독자적으로 갖는 주소공간. 가상으로 존재할 뿐, 실제로 어딘가에 연속적으로 할당되지는 않는다. 쪼개져서 Physical Memory에 올라가거나 Swap area에 들어감.
- 커널은 Physical memory에 항상 상주해 있다.
- Virtual Memory의 주소 공간을 Physical memory에 옮겨 놓는 것이 아니라 그때그때 필요한 코드를 뽑아서 메모리에 올린다.
- 사용자 프로그램의 경우 Physical Memory에서 더이상 사용되지 않는 부분은 무작정 지우는 것이 아니라 보관하고 있어야 하는 부분을 Swap area에 내려놓는다.
- Swap area는 Physical Memory의 연장공간이라고 생각하면 되고, 전원이 꺼지면 프로세스가 종료되기 때문에 Swap area에 있던 메모리는 쓸모가 없게된다.
- File system은 비휘발성, 파일 저장용도로 사용된다.
<br><br>
### 커널 주소 공간의 내용

![OS_3_2](https://user-images.githubusercontent.com/77624879/162800497-7254557d-f3ec-4044-8c18-4dcfa3ade954.png)

- code
    - 자원 관리를 위한 코드
    - 편리한 서비스를 제공을 위한 코드
    - 시스템콜, 인터럽트 처리 코드
- data
    - 운영체제가 사용하는 여러 자료구조가 정의되어 있음
    - CPU, Memory, Disk 등을 통제하므로 각 종류마다 자료구조를 만들어서 관리한다.
    - Process마다 해당 프로세스를 관리하기 위한 자료구조 또한 존재한다.(PCB, Process Control Block)
- stack
    - 함수를 호출하거나 리턴할 때 사용됨
    - 사용자 프로그램마다 커널 스택을 따로 두고 사용함
<br><br>
### 사용자 프로그램이 사용하는 함수

- 함수
    - 사용자 정의 함수
        - 자신의 프로그램에서 정의한 함수
    - 라이브러리 함수
        - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
        - 자신의 프로그램의 실행 파일에 포함되어 있음
    - 커널 함수
        - 운영체제 프로그램의 함수
        - 커널 함수의 호출 = 시스템 콜

*사용자 정의 함수와 라이브러리 함수는 사용자 프로그램 코드 영역에 정의되어 메모리 내에서 점프가 가능하지만, 커널함수는 사용자 프로그램 코드영역에 정의되어 있지 않아서 시스템 콜을 이용해야 한다.
<br><br>
### 프로그램의 실행(프로그램에서 CPU를 바라보았을 때 순서)
![Untitled](https://user-images.githubusercontent.com/77624879/162800539-34d38b27-4539-4f03-97e2-cb615147074d.png)
