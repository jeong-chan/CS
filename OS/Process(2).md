# Process(2)
<br><br>
### Thread-쓰레드

![img (1)](https://user-images.githubusercontent.com/77624879/162801720-be044e29-8420-4952-a776-d74567c2ec65.png)

- “A thread (or lightweight  process) is a basic unit of CPU utilization”
- 프로세스 하나에 CPU 수행 단위가 여러개 있는 것을 쓰레드 라고함. (PC가 여러개)
- CPU 수행단위가 여러개이기 때문에 각 쓰레드 별 함수를 리턴했던 정보를 저장할 stack 영역을 별도로 구분해서 사용
- Thread의 구성 (Thread별 독립적으로 갖는 부분)
    - Program Counter
    - register set
    - stack space
- Thread가 동료 thread와 공유하는 부분(=task)
    - code section
    - data section
    - OS resources
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

*보통 멀티쓰레드 = lightweight process     다중 멀티쓰레드 = heavyweight process
<br><br>
### Thread와 PCB

![993B51475A56452433](https://user-images.githubusercontent.com/77624879/162801748-9eec73a7-68a0-4c84-888b-25236df80d56.png)
<br><br>
### Thread 탄생이유

동일한 일을 처리하는 프로세스가 여러개 있다고 가정할경우, 주소공간이 그 프로세스의 수만큼 만들어지게 될것이고, 이것은 결국 메모리의 낭비를 가져오게 된다
<br><br>
### Thread의 장점

- 사용자의 입장에서 빠른 응답속도 - Responsiveness
    - 다중 스레드로 구성된 태스크 구조에서는 하나의 서버 쓰레드가 blocked (waiting) 상태인 동안에도 동일한 태스크 내의 다른 쓰레드가 실행(running) 되어 빠른 처리를 할 수있다.
- 자원 공유를 통한 성능 향상과 높은 처리율
    - 동일한 일을 수행하는 여러개의 프로세스를 사용하는 것보다 하나의 프로세스를 만들고 그 안에 CPU수행단위를 여러개 두게 되면 code, data, resource를 공유함으로써 자원을 효율적으로 사용하는 효과를 볼 수 있다.
    - 동일한 일을 수행하는 다중 쓰레드가 협력하여 자원의 낭비가 줄고 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
- 경제성이 좋다.
    - 프로세스를 만드는 것과 문맥교환은 오버헤드가 상당히 크다. 그러나 쓰레드를 만들거나 쓰레드간에 문맥교환은 굉장히 간단하다.
- 멀티 프로세서 환경에서의 병렬성 증가
    - CPU가 여러개 달린 컴퓨터에서만 얻을 수 있는 장점(이례적) 이다.
    - 각각의 CPU에서 쓰레드가 병렬적으로 작동하면서 병렬성이 증가한다.
    - 쓰레드를 사용하면 병렬성을 높일 수 있다.

![7](https://user-images.githubusercontent.com/77624879/162801781-770d620e-7aba-4a0c-bb6a-507cb9a5419a.png)
<br><br>
### Thread 구현 방법

- 커널 쓰레드
    - 쓰레드가 여러개 있다는 사실을 운영체제 커널이 알고 있다.
    - 쓰레드에서 쓰레드로 CPU가 넘어가는 것도 커널이 CPU 스케줄링 하듯이 넘겨줌
- 유저 쓰레드
    - 라이브러리를 통해서 지원된다.
    - 프로세스 안에 쓰레드가 여러개 있다는 것을 운영체제는 모른다.
    - 유저 프로그램이 스스로 라이브러리의 지원을 받아 여러 쓰레드를 관리한다.
    - 운영체제는 쓰레드의 존재를 모르기 때문에 구현상의 제약이 있을 수 있다.
- 실시간(real-time) 쓰레드
    - real-time 기능을 지원하는 쓰레드
