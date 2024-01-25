# 운영체제
## Chapter 3-3
### 프로세스 간 통신(IPC)
### Process Communication
- 프로세스들이 concurrently 하게 실행되는 경우
  - independent하게 실행
    - 공유하는 데이터도 없고 메세지 주고 받을 일도 없음
  - cooperating하게 실행(메세지를 주고받으려고 함)
    - 영향을 주거나 받을 때
    - 어떤 process가 데이터를 공유하고 메세지를 주고받을 때 문제가 발생함
    - 이 문제를 어떻게 해결할 것이냐 하는 것을 **IPC**라고 함

### IPC(Inter-Process Communication)
- Cooperating하는 process는 IPC 메커니즘이 필요함
  - IPC 메커니즘 data를 주고받는 것을 허용

- IPC는 2가지 방법이 있음
  - 공유 메모리 저장소를 사용하는 방법
  - 메세지를 주고받는 방법

### IPC in Shared-Memory Systems
- Producer-Consumer 문제
  - cooperating process들 간의 가장 기본적인 문제
  - 이 문제를 풀 수 있으면 가장 기본적인 문제를 풀 수 있음

  - producer는 정보를 생산하고 consumer는 정보를 소비하는 모델
  - 예시
    - compiler &rarr; assembly code 생산, assembler &rarr; 소비
    - web server &rarr; HTML file 생산, browser &rarr; 소비
  
- Shared-memory 사용
  - producer와 consumer가 공유하고 있는 영역
  - 중간에 buffer를 사용(대부분 bounded buffer)
    - producer : buffer를 계속해서 채울 수 있음
    - consumer : buffer를 계속해서 비울 수 있음

  - 이 buffer를 shared-memory로 만듬

### IPC in Message-Passing Systems
- shared-memory의 스키마
  - 메모리 영역을 공유가 요구된다.
  - shared-memory의 코드들을 쓰고 accessing해주는 것을 programmer가 다 해줘야함.

#### Message-Passing
- message를 주고받는 process들에게 O/S가 수단을 제공해 달라는 것
- 처리를 다 O/S에게 맡김

- message-passing의 facility
  - send(message)
    - send(next-produced)
  - receive(message)
    - receive(next_consumed)

#### Communication Links
- P와 Q의 2가지 process가 통신할 때 send와 message 2가지 통신만 하면 됨
- Communication Links를 사용하면 implemented가 복잡해짐
  - direct
    - 각 process가 communication하는 상대 process를 아는 것
    - recipient와 sender를 명시적으로 이름을 붙임
    - 예시
      - send(P, message) : P에게 메세지를 보낸다 
      - receive(Q, message) : Q가 메세지를 받는다
  - indirect
    - 중간에 매게체가 필요함
    - mailboxes(ports)로 보내고 받는다
    - mailbox를 이용할 때만 링크가 생긴다.
    - 2개 이상의 process가 associate해도 문제되지 않음
    - 다른 많은 links들이 존재할 수 있음.
    - 예시
      - send(A, message) : A(mailbox)에게 메세지를 보낸다
      - receive(A, message) : A(mailbox)가 메세지를 받는다
    - OS가 indirect한 link를 만들 때 제공해줘야하는 mechanism
      - 새로운 mailbox 만들기
      - mailbox를 통한 send, receive 기능 만들기
      - mailbox 지우기
  
  - synchronous
    - Blocking send : message를 받을 때 까지 send를 block
    - Blocking receive : message가 available 해질 때 까지 receive를 block
  - asynchronous
    - Non-blocking send : message를 보내기만 하고 다음 작업 continue
    - Non-blocking receive : message가 valid 하든 null 이든 다 받음

  - automatic buffering
  - explicit buffering

## Chapter 3-4
### IPC system의 예시
- Shared Memory : POSIX Shared Memory
  - POSIX(Portable Operating System Interface) for uniX
    - O/S 의 표준화
- Message Passing : Pipes
  - UNIX에서는 주로 pipes를 사용

#### POSIX shared memory
- memory-mapped files를 이용해서 buffer를 잡음
  - shared memory file을 memory에 mapping 시켜서 memory를 만듬

- shared memory 만드는 방법
  ```c
  fd = shm_open(name, O_CREAT | ORDWR, 0666);
  ftruncate(fd, 4096);
  mmap(0, SIZE, PROT_READ | PROT_WIRTE, MAP_SHARED, fd, 0);
  ```
  1. shared-memory object를 만듬
  2. object의 사이즈를 byte 기준으로 설정
  3. memory-mapped file을 shared-memory에 mapping 시켜줌

```c
// producer
  #include <stdio.h> 
  #include <stdlib.h> 
  #include <string.h> 
  #include <fcntl.h> 
  #include <sys/shm.h> 
  #include <sys/stat.h> 
  #include <sys/mman.h>
int main()
{
  const int SIZE = 4096;                        // the size of shared memory
  const char *name = "OS";                      // the name of shared memory
  
  const char *message_0 = "Hello, "; 
  const char *message_1 = "Shared Memory!\n";

  int shm_fd;                                   // the file descriptor of shared memory
  char *ptr;                                    // pointer to shared memory

  /* create the shared memory object */ 
  shm_fd= shm_open (name, O_CREAT | O_RDWR, 0666);
  /* configure the size of the shared memory */ 
  ftruncate (shm_fd, SIZE);
  /* map the shared memory object */
  ptr = (char *)mmap(0, SIZE, PROT_READ|PROT_WRITE, MAP_SHARED, shm_fd, 0);
  /* write to the shared memory */ 
  sprintf(ptr, "%s", message_0); 
  ptr += strlen(message_0); 
  sprintf(ptr, "%s", message_1); 
  ptr += strlen(message_1);
                                  $gcc 3.16_shm_producer.c -lrt
  return 0;
}
```

```c
// consumer
  #include <stdio.h> 
  #include <stdlib.h> 
  #include <string.h> 
  #include <fcntl.h> 
  #include <sys/shm.h> 
  #include <sys/stat.h> 
  #include <sys/mman.h>
int main()
{
  const int SIZE = 4096;                        // the size of shared memory
  const char *name = "OS";                      // the name of shared memory

  int shm_fd;                                   // the file descriptor of shared memory
  char *ptr;                                    // pointer to shared memory

  /* create the shared memory object */ 
  shm_fd= shm_open(name, O_RDONLY, 0666);
  /* map the shared memory object */
  ptr = (char *)mmap(0, SIZE, PROT_READ|PROT_WRITE, MAP_SHARED, shm_fd, 0);
  /* read from the shared memory object */
  printf("%s", (char *)ptr);

  /* remove the shared memory */
  shm_unlink(name);
                                  $gcc 3.16_shm_consumer.c -lrt
  return 0;
}
```

### Pipes
- UNIX에서 초창기에 사용하던 IPC 매커니즘
- 2개의 process가 communication하는 도구 처럼 행동함

- unidirectional
- bidirectional

- half-duplex
- full-duplex

- relationship O
- relationship X

- network에서 동작할 수 있는가
  - pipe는 보통 network에서는 잘 안씀
  - network에서 쓸 수 있는 pipe를 *socket*이라 부름

- 2개의 pipe type
  - ordinary pipes
    - Parent가 pipe를 create하면 child하고 communication하는데 사용함
    - 2개의 process가 producer-consumer fashion으로 만들어짐
      - producer가 write
      - consumer가 read
      - unidirectional : one-way communication이 가능
      - two-way communication을 하려면 2개의 pipe를 사용
    - UNIX system
      - pipe라는 system call 사용

```c
  #include <stdio.h> 
  #include <string.h> 
  #include <unistd.h> 
  #include <sys/types.h>

  #define BUFFER_SIZE 25 
  #define READ_END
  #define WRITE_END 1
  int main()
  {
    char write_msg[BUFFER_SIZE] = "Greetings"; 
    char read_msg[BUFFER_SIZE];
    int fd[2];
    pid_t pid;

    /* create the pipe */ 
    pipe(fd);
    
    pid = fork(); // fork a new process

    if (pid > 0) { // parent process 
      close(fd[READ_END]);
      /* write to the pipe */
      write(fd[WRITE_END], write_msg, strlen(write_msg) + 1);
      close (fd[WRITE_END]);
    }
    else if (pid == 0) { // child process
      close(fd[WRITE_END]);
      /* read to the pipe */
      read(fd[READ_END], read_msg, BUFFER_SIZE);
      printf("read %s\n", read_msg);
      close(fd[READ_END]);
    }
    return 0;
  }
```
  - Named pipes
    - pipe에 이름을 붙여줄 수 있는 것
    - parent-child relationship이 없어도 사용 가능

### Two other strategies in client-server systems
- Sockets
  - 통신하기 위한 양종단을 얘기함
  - 2개의 원격에 있는 컴퓨터끼리의 연결을 의미하는 pipe 형태의 connection
    - 양쪽의 computer를 특정 &rarr; ip address
    - 각 computer끼리 연결된 pipe를 특정 &rarr; port
    - ip address + port = socket

  - 양쪽의 bit나 방식이 다르면 문제가 많이 생김
    - 그 때문에 생긴 것이 RPCs

  - 예시
    - https://www.google.co.kr:8000
    - 앞에 있는게 ip address
    - 8000이 port
    - 둘을 합쳐놓은 것이 socket

#### RPCs(Remote Procedure Calls)
- 원격에 있는 network된 process들 간의 원격호출을 저장
- 라이브러리 개념이랑 비슷...?

#### Java provides
- java가 소켓을 편하게 사용하게 하기 위해 제공한 library
  - socket class : connection-oriented (TCP)
  - DatagramSocket class : connectionless(UDP)
  - MulticastSocket class : multiple recipients 

```java
import java.net.*;
import java.io.*;

// Data server
public class DateServer { 

  public static void main(String[] args) throws Exception {
    ServerSocket server = new ServerSocket(6013);

    /* Now listen for connections */
    while (true) {
        Socket client = server.accept();
        PrintWriter pout = new PrintWriter(client.getOutputStream(), true);
        
        /* write the Date to the socket */            
        pout.println(new java.util.Date().tostring());

        /* close the socket and resume listening for connections */
        client.close();
    }
  }
}

// Data client
public class DateClient { 
  
  public static void main(String[] args) throws Exception {
    /* make connection to server socket */
    Socket socket = new Socket("127.0.0.1", 6013);

    InputStream in = socket.getInputStream();
    BufferedReader br = new BufferedReader(new InputStreamReader(in));

    /* read date from the socket */
    String line = null;
    while ((line = br.readLine()) != null) 
        System.out.println(line);

    /* close the socket connections */
    socket.close();
  } 
}
```

### RPC(Remote Procedure Call)
- 예제 만들기도 복잡함
- 현업에 가서 전문가가 되면 되게 많이 씀
  - Java에서는 RMI로 구분
  - Microsoft 에서는 COM, DCOM, CORBA, EJB로 구분

- IPC의 확장 개념
  - IPC는 컴퓨터 내부에서 2개의 process가 서로 통신하는 것
  - RPC는 굳이 내부에서 2개의 process가 있을 필요 없이 원격으로 연결되어 있는 컴퓨터끼리 통신할 수 있는 것
- remote service의 공통적인 형태
- procedure를 원격 지원하는 함수를 호출하는 것을 RPC라 함

- marshaling
  - 원격 서비스를 이용하는 2개의 api끼리 주고받는 데이터를 정렬하는 것