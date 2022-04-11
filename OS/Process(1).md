# Process(1)
<br><br>
### 프로세스의 개념

- “Process is a program in execution” = 실행중인 프로그램
- 프로세스의 문맥(context)
    - 프로세의 현재 상태를 나타내는데 필요한 모든 요소
    - 크게 3가지로 나누어 설명할 수 있음
    1. CPU 수행 상태를 나타내는(CPU와 관련된) 하드웨어 문맥
        - Program Counter
        - 각종 register
    2. 프로세스의 주소 공간
        - code, data, stack에 어떤 내용이 들어있는가
    3. 프로세스 관련 커널 자료 구조
        - PCB(Process Control BlocK)
        - Kernel Stack
    
    ![os_process_concept](https://user-images.githubusercontent.com/77624879/162800949-05b08fb6-51c5-468f-9c65-e12f3818f0b7.png)

<br><br>
### 프로세스의 상태(Process State)
- Suspended X <br>
![content07](https://user-images.githubusercontent.com/77624879/162801044-1acbd539-fad5-474e-ba75-7996ed8e1698.png)
              
- Suspended O <br>
![Untitled (6)](https://user-images.githubusercontent.com/77624879/162801327-d031317c-6153-4335-9dd1-920788431518.png)


- 프로세스는 상태(state)가 변경되며 수행된다.
    - Running : CPU를 잡고 instruction을 수행중인 상태
    - Ready : CPU를 기다리는 상태(메모리 등 다른 조건을 모두 만족함)
    - Blocked(wait, sleep)
        - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
        - Proccess자신이 요청한 event가  즉시 만족되지 않아 이를 기다리는 상태
        - 자신이 요청한 event가 만족되면 Ready 상태가 됨
        - ex) 디스크에서 file을 읽어와야 하는 경우,  I/O, 다른 프로세스가 공유데이터를 사용하는 것을 기다려야하는 상태 등
    - New : 프로세스가 생성중인 상태
    - Terminated : 수행(execution)이 끝나서 종료중인 상태(종료가 완료되면 더이상 프로세스가 아님)
    - Suspended (stopped)
        - 외부(사용자 or 중기 스케줄러)적인 이유로 프로세스의 수행이 정지된 상태
        - 프로세스는 통째로 디스크에 swap out 된다.
        - 외부에서 resume 해주어야 Active
        - Inactive 상태인 Suspended Blocked에서 진행중이었던 I/O등이 완료되면 Suspended Ready 상태로 바뀔 수 있다
        - ex) 사용자가 프로그램을 일시 정지 시킨 경우(break key), 시스템이 여러 이유로 프로세스를 잠시 중단시킴 → 그 이유중 하나가 메모리에 너무 많은 프로세스가 올라와 있을 때 중기 스케줄러가 Memory를 빼앗은 경우

![os_process_state_2](https://user-images.githubusercontent.com/77624879/162801377-c51de7e0-f78f-46e7-93b0-c721ca8be41f.png)

*운영체제는 커널의 data 영역에서 queue자료구조를 만든 뒤 프로세스를 분배하는 것이고, 모든 queue가 선입선출은 아님. 기준에 따라서 늦에 온것이 먼저 CPU를 잡기도 함.
<br><br>
### Process Control Block(PCB)

![image](https://user-images.githubusercontent.com/77624879/162801420-260149fb-55a9-4562-8f69-66fddb849ba3.png)

- 운영체제가 각 프로세를 관리하기 위해 프로세스당 유지하는 정보
- 다음의 구성 요소를 갖는다(구조체로 유지)
    - OS가 관리상 사용하는 정보
        - Process state, Process ID
        - scheduling information, priority
    - CPU 수행 관련 하드웨어 값
        - Program counter, registers
    - 메모리 관련
        - Code, data, stack의 위치정보
    - 파일 관련
        - Open file descriptors...
<br><br>
### 문맥 교환(Context Switch)

- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에게 넘어갈 때 운영체제는 다음을 수행
    - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
    - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어옴

![8879a93a-01d9-4829-bbc0-7bb01a05ac7c](https://user-images.githubusercontent.com/77624879/162801447-84907c57-ad15-4888-a49e-7822cd53dbeb.png)

- System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님. (1)의 경우 CPU가 원래의 프로세스로 돌아가기 때문. 단순히 커널모드로 변했다가 사용자모드로 변한 것으로 볼 수 있음

![9998B24E5A54208B1C](https://user-images.githubusercontent.com/77624879/162801481-85114e33-c8dd-458c-a70c-d6ddf9c33893.png)

*(1)의 경우도 CPU 수행 정보 등 context의 일부를 PCB에 save 해야함. 그러나, 문맥교화을 하는

(2)의 경우 그 부담이 훨씬 큼(eg. cache memory flush)

**cache memory flush : 프로세스A에서 프로세스B로 문맥교환이 일어나면 프로세스A가 사용하던 cache memory를 모두 지워야함. 이것이 상당한 오버헤드가 될 수 있어서 (2)의 경우에서 부담이 훨씬 큰 것임
<br><br>
### 프로세스를 스케줄링하기 위한 큐

- 프로세스들은 각 큐들을 오가며 수행된다.
- Job queue : 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready queue : 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device queues : I/O device의 처리를 기다리는 프로세스의 집합

![img](https://user-images.githubusercontent.com/77624879/162801503-17452235-3c35-470c-a378-be7717e77f28.png)

- PCB에 있는 Pointer를 이용하기 때문에 큐 내에서 줄 서기가 가능한 것임
<br><br>
### 프로세스 스케줄링 큐의 모습

![995B023A5A755E1924](https://user-images.githubusercontent.com/77624879/162801532-77bbe29c-bc66-4623-a90e-5f06659b4520.png)

<br><br>
### **스케줄러(Scheduler)**

- **Long-term scheduler**(장기 스케줄러 or job scheduler)
    - 시작 프로세스(new) 중 어떤 것들을 ready queue로 보낼지 결정
    - 프로세스에 memory(및 각종 자원)를 주는 문제
    - degree of Multiprogramming을 제어
    - **time sharing system에는 보통 장기 스케줄러가 없음 (무조건 ready)**
- **Short-term scheduler**(단기 스케줄러 or CPU scheduler)
    - 어떤 프로세스를 다음번에 running 시킬지 결정
    - 프로세스에 CPU를 주는 문제
    - 충분히 **빨라야 함**(millisecond 단위)
- **Medium-term Scheduler**(중기 스케줄러 or Swapper)
    - **여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫓아냄**
    - 프로세스에게서 memory를 뺏는 문제
    - degree of Multiprogramming을 제어 (Long-term scheduler가 없는 경우)
    

*degree of Multiprogramming : 메모리에 여러 프로그램이 동시에 올라가있는것을 나타냄(메모리에 올라간 프로그램의 수)

**메모리에 올라간 프로그램의 수가 너무 많거나 너무 없으면 성능이 떨어져서 조절해야할 필요가 있음

***메모리에 올라간 프로그램의 수가 너무 적다? 프로그램이 CPU를 쓰다가 I/O등의 이벤트가 생기면 CPU가 놀아버리는 현상이 발생할 수 있음

****메모리에 올라간 프로그램의 수가 너무 많다? 각 프로그램이 메모리를 너무 조금씩 가지고 있어 당장 필요한 부분이 메모리에 안 올라와 있을 수 있음. 그렇게 되면 메모리를 가지러 가야함(I/O 등)
