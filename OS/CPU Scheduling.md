# CPU Scheduling

### CPU and I/O Bursts in Program Execution

![Untitled](https://user-images.githubusercontent.com/77624879/162802595-d36314cc-5a36-44ba-bc84-80858a4cef37.png)

- 어떤 프로그램이든지 위 와 같은 절차를 실행하며 프로그램 실행이 진행됨
- CPU burst : CPU만 연속적으로 쓰면서 instruction을 실행하는 단계
- I/O burst : I/O를 실행하고 있는 단계
- 모든 프로그램은 CPU burst와 I/O burst가 반복되는 절차를 갖는다.
- 단, 프로그램마다 burst의 빈도수는 다르다.
- 주로 사람이 interaction을 하는 프로그램이 CPU burst, I/O burst 의 반복 빈도가 높다.
- 반면, 과학/계산용 프로그램은 CPU brust의 실행 시간이 길다.
<br><br>
### CPU-burst Time의 분포

![image (1)](https://user-images.githubusercontent.com/77624879/162802617-72da97c8-f495-4b2b-ac63-137e90072ea9.png)

- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요하다.
    - Interactive job에게 적절한 response 제공 요망
    - CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용
<br><br>
### 프로세스의 특성 분류

- 프로세스는 그 특성에 따라 다음 두 가지로 나눔
    - I/O-bound process
        - I/O bound job
        - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한  job
        - CPU를 짧게 쓰고 I/O가 끼어드는 종류의 job
        - many shorts CPU burtsts
        - CPU 사용 빈도가 높고, CPU 사용시간이 짧다.
    - CPU-bound process
        - CPU bound job
        - CPU만 굉장히 오래 쓰는 종류의 job
        - 계산 위주의 job
        - fow very long CPU bursts
        - CPU 사용 빈도가 적고, CPU 사용시간이 길다.
        
<br><br>
### CPU Scheduler & Dispatcher

- **CPU Scheduler**
    - 운영체제 안에 CPU Scheduling하는 코드 부분
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고른다.
- **Dispatcher**
    - CPU 제어권을 넘기는 커널 코드 부분
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘긴다.
    - 이 과정을 context switch(문맥 교환)라고 한다.
- CPU 스케줄링이 필요한 경우는 프로세스에게 다음과 같은 상태 변화가 있는 경우이다.
    1. Running → Blocked (예: I/O 요청하는 시스템 콜)
    2. Running → Ready (예: 할당시간만료로 timer interrupt)
    3. Blocked → Ready (예: I/O 완료 후 인터럽트)
        - 우선순위에 따른 스케줄링 시, 우선순위가 제일 높은 프로세스가 I/O를 시작할 때 Blocked, I/O가 끝나고 우선순위가 제일 높은 프로세스로 되돌아가며 Ready
    4. Terminate
        - 프로세스 종료로 인해 다른 프로세스로 CPU를 넘겨야하는 경우

*1번과 4번에서의 스케줄링은 nonpreemptive (=강제로 빼앗지 않고 자진 반납, 비선점형)

**All other scheduling is preemptive(=강제로 빼앗음, 선점형)
<br><br>
### Scheduling Algorithms

- FCFS(First-Come First-Served)
- SJF(Shortest-Job-First)
- SRTF(Shortest-Remaining-Time-First)
- Priority Scheduling
- RR(Round Robin)
- Multilevel Queue
- Multileve Feedback Queue
<br><br>
## Scheduling Criteria

*Performance Index(=Performance Measure, 성능 척도)
<br><br>
### 시스템 입장의 성능 척도

- 이용률(CPU utilization)
    - keep the CPU as busy as possible(CPU를 가능한 바쁘게 유지)
    - 전체 시간 중에서 CPU가 놀지않고 일한 시간의 비율
- 처리량(Throughtput)
    - '#' of processes that complete their execution per time unit
    - 주어진 시간동안에 몇 개의 일을 처리했는가
<br><br>
### 프로세스 입장의 성능 척도(시간과 관련)

- 소요시간,반환시간(Turnaround time)
    - amount of time to execute a particular process
    - CPU를 얻기위해 들어와서 기다리고, CPU를 얻어서 다 쓰고 나갈때까지 걸린 시간
- 대기 시간(Waiting time)
    - amount of time a process has been waiting in the ready queue
    - CPU를 받기 위해 기다린 시간
    - 한번의 CPU burst 동안에도 여러차례 waiting이 발생할 수 있으며, waiting시간의 총 합
- 응답 시간(Response time)
    - amount of time it takes from when a request was submitted until the first respose is produced, **not** output (for time-sharing environment)
    - Ready Queue에 들어와서 처음으로 CPU를 얻기까지 걸린 시간
    - 시분할 환경에서는 처음으로 CPU를 얻는 시간이 사용자 응답과 관련해서 굉장히 중요함
<br><br><br><br>
### FCFS(First-Come First-Served)

- 프로세스의 도착순서가 빠른 것부터 처리(선입선출)
- 비 선점형
- 효율적인 방법은 아님(CPU 사용시간이 긴 프로세스가 일찍 온 경우)

![fcfs](https://user-images.githubusercontent.com/77624879/162802637-eb981f00-95a9-4521-964e-e1d2a4f0002e.jpg)

- 어떤 프로세스가 먼저 오는가에 따라 결과가 매우 다름
- Convoy effect : CPU사용 시간이 짧은 프로세스가 CPU 사용 시간이 긴 프로세스 뒤에 오는 현상
<br><br>
### SJF(Shortest-Job-First)

- 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용
- CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄링
- Two schemes:
    - **Nonpreemptive**
        - 일단 CPU를 집으면 이번 CPU burst가 완료될 때까지 CPU를 선점(preemption) 당하지 않음
        
        ![sjf non](https://user-images.githubusercontent.com/77624879/162802683-420b62fc-da84-470e-aa87-578ce92f6aeb.png)

    - **Preemptive**
        - 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김
        - 이 방법을 Shortest-Remaining-Time-First(SRTF) 라고도 부름
        - 주어진 프로세스들에 대해 모든 알고리즘 중에서 minimum average waiting time을 보장함(SJF is optimal)
        
        ![sjf preem](https://user-images.githubusercontent.com/77624879/162802705-917f66ed-c19e-4588-b4fe-1ff11c889360.png)

- SJF의 문제점
    - Starvation(기아현상) : CPU사용시간이 긴 프로세스는 CPU를 영원히 못 받을 수도 있음
    - CPU Burst Time를 미리 알 수 없음
    
<br><br>
### 다음 CPU Burst Time의 예측

- 과거의 CPU burst time을 이용해서 추정(estimate)만 가능함
- Exponential averaging

<img width="390" alt="exponential" src="https://user-images.githubusercontent.com/77624879/162802722-edb84bda-c949-4212-becc-3cc1f9064a8b.png">

![exponential2](https://user-images.githubusercontent.com/77624879/162802790-5b0b643f-f14e-4f3a-9d03-8964838f4444.jpg)

- a(알파)가 0 또는 1인 경우는 극단적인 경우라고 볼 수 있음
<br><br>
### Priority Scheduling

- A **priority number** (integer) is associated with each process
- highest priority를 가진 프로세스에게 CPU를 할당(smallest integer = highest priority)
- Two Schemes
    - Preemptive
    - nonpreemptive
- SJF는 일종의 priority scheduling이다.
    - priority = predicted next CPU burst time
- 문제점
    - Starvation(기아 현상)
- 해결법
    - Aging(에이징, 노화): 시간이 길어질 수록 프로세스의 우선순위를 조금씩 높여줌.
<br><br>
### Round Robin (RR)

- 현대적인 컴퓨터 시스템에서 사용하는 스케줄링 기법의 기반이 되는 알고리즘
- 각 프로세스는 동일한 크기의 할당 시간(time quantum)을 가짐
    - 일반적으로 10-100 milliseconds
- 할당 시간이 지나면 프로세스는 선점당하고 ready queue의 제일 뒤에 가서 다시 줄을 선다
- n 개의 프로세스가 ready queue에 있고 할당 시간이 q(time unit)인 경우 각 프로세스는 최대 q (time unit) 단위로 CPU 시간의 1/n을 얻는다.
    - 어떤 프로세스도 (n-1)q 이상 기다리지 않는다.
- 응답 시간이 빠르다.
- 대기 시간은 프로세스가 CPU를 사용하려는 시간에 비례해서 증가한다.
- 일반적으로 SJF보다 average turnaround time이 길지만, response time은 더 짧다.
- CPU사용시간을 모두 알 수 없는 경우 일반적으로 사용하기 좋음
- 가끔씩 CPU사용시간이 모두 동일하고 time unit이 짧은 경우 일정 시점이 되었을 때, 프로세스가 거의 동시에 빠져나감(모든 프로세스가 waiting time을 길게 갖게 됨)
- Performance
    - q large(할당시간이 매우 큰 경우) ⇒ FCFS
    - q small(할당시간이 매우 작은 경우) ⇒ context switch가 여러번 발생. 오버헤드가 증가
- 예제(q가 20인 경우)

![RR](https://user-images.githubusercontent.com/77624879/162802891-f0030299-95fd-4e87-89b6-08d58f42be70.jpg)
<br><br>
### Multilevel Queue

![1](https://user-images.githubusercontent.com/77624879/162802917-f405898b-0963-4b6c-843a-202869c67f7f.png)

- 맨 위에서 아래로 내려올 수록 우선순위가 낮고, 우선순위는 바뀌지 않음
    - system processes : 시스템과 관련된 프로세스
    - interactive processes : 사람과 interaction 하는 프로세스
    - batch processes : cpu만 오래동안 사용하는 프로세스
- Ready queue를 여러 개로 분할
    - foreground queue**(interactive)**
    - background queue**(batch - no human interaction)**
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground-**RR(빠른 응답을 위함)**
    - background-**FCFS(문맥교환 overhead를 줄이기 위함)**
- 큐에 대한 스케줄링이 필요
    - **Fixed priority scheduling(우선순위 스케줄링)**
        - serve all from foreground then from background
        - 기아현상이 발생할 수도 있음
    - **Time slice**
        - 각 큐에 CPU time을 적절한 비율로 할당
        - Eg.. 80% to foreground in RR, 20% to background in FCFS
<br><br>
### Multilevel Feedback Queue

![mfg](https://user-images.githubusercontent.com/77624879/162802939-c04668a0-fac1-463b-bffc-ae5c7cf215c7.jpg)

- 맨 위에서 아래로 내려올 수록 우선순위가 낮고, 우선순위는 바뀔 수 있음
- 프로세스가 다른 큐로 이동 가능
- 에이징(aging)을  이와 같은 방식으로 구현할 수 있다.
- Multilevel-feedback-queue scheduler를 정의하는 파라미터 들
    - Queue의 수
    - 각 큐의 scheduling algorithm
    - Process를 상위 큐로 보내는 기준
    - Process를 하위 큐로 보내는 기준
    - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준
- RR으로 time unit(q)을 점점 늘려가다가 마지막에 FCFS 방식으로 처리
- 예시)

![exmfq](https://user-images.githubusercontent.com/77624879/162802975-f24c5489-0f86-4732-b4ea-5e7d607f5c1e.jpg)
<br><br>
### Multiple-Processor Scheduling

- CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous processor인 경우
    - Queue에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우에는 문제가 더 복잡해짐
- Load sharing
    - 특정 CPU만 일하는것을 방지하도록 함.
    - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘이 필요
    - 별개의 큐를 두는 방법 / 공동 큐를 사용하는 방법
- Symmetric Multiprocessing(SMP)
    - 모든 CPU가 대등함.
    - 각 프로세서가 각자 알아서 스케줄링 결정
- Asymmetric multiprocessing
    - 여러개의 CPU중에서 하나의 CPU가 전체적인 control을 담당
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름
<br><br>
### Real-Time Scheduling

- deadline이 있기 때문에 미리 스케줄링을 해놓고 job을 배치해주는 방식을 많이 씀
- Hard real-time systems
    - deadline을 반드시 보장해야하는 시스템에서 사용됨
    - Hard real-time task는 정해진 시간 안에 반드시 끝내도록 스케줄링해야 함
- Soft real-time computing
    - 일반적인 time-sharing 시스템에서 섞여서 사용됨
    - Soft real-time task는 일반 프로세스에 비해 높은 priority를 갖도록 해야함
<br><br>
### Thread Scheduling

- Local Scheduling
    - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
    - 운영체제는 thread의 존재를 모르기 때문에 process에 CPU를 전달, process가 thread에 CPU를 줄지 결정
- Global Scheduling
    - Kernel level thread의 경우 일반 프로세스와 마찬 가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정
    - 운영체제가 thread의 존재를 알고 있기 때문에, 특정 알고리즘을 통해 스케줄링
<br><br>
### Algorithm Evaluation

- 어떤 알고리즘이 좋은지 평가하는 방법
- Queueing models
    - 굉장히 이론적인 방법, 예전에 많이 사용했었음
    - 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산
- Implemenatation(구현) & Measurement(성능 측정)
    - 실측하는 방법. 굉장히 어려움.
    - 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능을 측정 비교
- Simulation(모의 실험)
    - 실측이 어려운 경우 대체로 사용가능
    - 알고리즘을 모의 프로그램으로 작성후 trace를 입력으로 하여 결과 비교
    - 실제 프로램에서 CPU burst등을 근거로 할수록 신빙성이 높아짐
    
    *trace : simulation프로그램의 input으로 들어감. 실제 프로그램을 통해서 추출한 input data를 이용하여 실제 상황을 더 잘 반영할 수 있으며, 임의로 만들 수도 있음.
