# Process Management

### 프로세스 생성(Process Creation)

- 부모 프로세스(Parent process)가 자식 프로세스(children process)생성 → 복제생성,여러개 가능
- 프로세스의 트리(계층 구조) 형성
- 프로세스는 자원을 필요로 함
    - 운영체제로부터 받는다.
    - 부모와 공유한다.
- 자원의 공유 → 원칙적으로는 자원을 공유하지는 않음.
    - 부모와 자식이 모든 자원을 공유하는 모델
    - 일부를 공유하는 모델
    - 전혀 공유하지 않는 모델
    
    *Copy-on-Write(COW) 기법: Write가 발생했을 때, Copy를 하는 기법. 
    
    자식은 부모를 복제하기 때문에 같은 내용의 자원을 사용하더라도 메모리가 낭비 됨. 하지만, 리눅스 등의 일부 운영체제에서는 무조건 카피하는 것이 아니라 자원을 공유하다가 자식과 부모 프로세스의 차이점이 발생하면 공유하던 메모리 공간을 **일부 카피**해서 자식이 갖게 함으로써 메모리 낭비를 줄임.
    
- 수행(Execution)
    - 부모와 자식은 공존하며 수행되는 모델
    - 자식이 종료(terminate)가 될 때까지 부모가 기다리는(wait,blocked) 모델
- 주소 공간(Address space)
    - 자식은 부모의 공간을 복사함(binary and OS data)
    - 자식은 그 공간에 새로운 프로그램을 올림
- 유닉스의 예
    - fork() 시스템 콜이 새로운 프로세스를 생성
        - 부모를 그대로 복사 (OS data except PID + binary)
        - 주소 공간 할당
    - fork() 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림
- 프로세스의 생성 2단계
    1. 부모 프로세스를 복제하는 단계
    2. 그 위에 덮어 씌우는 단계
    - 독립적이기 때문에, 복제만하고 덮어씌우지 않을 수도 있음
    - 자식프로세스를 만들지 않고 exec()을 하면 완전히 새로운 프로세스로 바뀔 수도 있음
<br><br>
### 프로세스 종료(Process Termination)

- 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌(exit) →자발적 종료
    - 자식이 부모에게 output data를 보냄(via wait).
    - 프로세스의 각종 자원들이 운영체제에게 반납됨.
- 부모 프로세스가 자식의 수행을 종료시킴(abort) → 비자발적, 강제 종료
    - 자식이 할당 자원의 한계치를 넘어섬
    - 자식에게 할당된 태스크가 더 이상 필요하지 않음
    - 부모가 종료(exit)하는 경우
        - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않는다.
        - 단계적인 종료
        - 부모 프로세스는 항상 자식 프로세스가 종료되고 난 후에 종료됨.
<br><br>
### fork() 시스템 콜

- A process is created by the fork() system call
    - creates a new address space that is a duplicate of the caller

```c
int main()
{ int pid;
	pid = fork();
	if(pid == 0)     //자식 프로세스 (fork의 결과값 '0')
		printf("\n Hello, I am child!\n");
	else if (pid > 0)   //부모 프로세스 (fork의 결과값 '양수')
		printf("\n Hello, I am parent!\n");
}
```

- 복제되어 생겨난 자식 프로세스는 fork(); 다음의 구문부터 시작함
    - 부모의 ProgramCounter까지 복제하기 때문에 Main함수의 처음 부터 시작하는 것이 아님.
    - 만약 fork()위에 다른 코드가 있어도 자식 프로세스는  fork();의 윗 부분을 실행할 수 없다.
<br><br>
### exec() 시스템 콜

- A process can execute a different program by the exec() system call.
    - replaces the memory image of the caller with a new program.

```c
int main()
{ int pid;
	pid = fork();
	if(pid == 0)     //자식 프로세스 (fork의 결과값 '0')
	{	printf("\n Hello, I am child! Now I'll run date\n");
		execlp("/bin/date", "/bin/date", (char*)0);
		printf("hi");
	}	
	else if (pid > 0)   //부모 프로세스 (fork의 결과값 '양수')
		printf("\n Hello, I am parent!\n");
}
```

- exec()을 수행하여 덮어쓰기 된 프로세스는 완전히 처음부터 실행되며, 원래 코드로 돌아오지 않기 때문에, exec() 다음 부분을 실행할 수 없음.
    
    → ex) printf(”hi”)는 실행되지 않는다.
    

*execlp(”프로그램이름”,”프로그램이름”,”아규먼트”,”널포인터”); 의 형태로 사용함.
<br><br>
### wait() 시스템 콜

- 프로세스 A가 wait() 시스템 콜을 호출하면
    - 커널은 child가 종료될 때까지 프로세스 A를 sleep시킨다.(block 상태)
    - Child process가 종료되면 커널은 프로세스 A를 깨운다.(ready 상태)

![image](https://user-images.githubusercontent.com/77624879/162802078-84286811-229c-4694-896a-7ab6a4f54338.png)
<br><br>
### exit() 시스템 콜

- 프로세스의 종료
- 자발적 종료
    - 마지막 statement 수행 후 exit() 시스템 콜을 통해서 종료
    - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
- 비 자발적 종료
    - 부모 프로세스가 자식 프로세스를 강제 종료시킴
        - 자식 프로세스가 한계치를 넘어서는 자원 요청
        - 자식에게 할당된 태스크가 더 이상 필요하지 않음
    - 키보드로 kill, break 등을 친 경우
    - 부모가 종료하는 경우
        - 부모 프로세스가 종료하기 전에 자식들이 먼저 종료 됨
        
<br><br>
### 정리: 프로세스와 관련한 시스템 콜

- fork() : create a child (copy)
- exec() : overlay new image
- wait() : sleep until child is done
- exit() : frees all the resources, notify parent
<br><br>
### 프로세스 간 협력

- 독립적 프로세스(Independent process)
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
- 협력 프로세스(Cooperating process)
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
- 프로세스 간 협력 메커니즘(IPC : Interprocess  Communication, 프로세스 간 정보를 주고받는 방법)
    - 메시지를 전달하는 방법
        - message passing : 커널을 통해 메시지 전달
    - 주소 공간을 공유하는 방법
        - shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 shared memory 메커니즘이 있음

*thread: thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 process를 구성하는 thread들 간에는 주소 공간을 공유하므로 협력이 가능하다.
<br><br>
### Message passing

- Message System
    - 프로세스 사이에 공유 변수(shared variable)를 일체 사용하지 않고 통신하는 시스템
- Direct Communication
    - 통신하려는 프로세스의 이름을 명시적으로 표시
    
   ![img](https://user-images.githubusercontent.com/77624879/162802106-e8062900-c223-4071-bb36-c6a7ed17d274.png)

- Indirect Communication
    - mailbox (또는 port)를 통해 메시지를 간접 전달
    
    ![다운로드](https://user-images.githubusercontent.com/77624879/162802137-b65d28bf-7d3c-4145-b071-676e4f6def7e.png)

<br><br>
### IPC : Interprocess Communication
![img (2)](https://user-images.githubusercontent.com/77624879/162802163-40e485b7-0f31-47db-aa53-a83b8f210216.png)
