## Thread
lightweight process(LWP)  
&rarr; 하나의 process 안에서 동시에 여러 작업을 수행 가능  

• a basic unit of CPU utilization(가장 기본적인 CPU 점유 단위)  
• <b>thread ID, program counter, register set, stack</b> 공유   
&rarr; process(PID) 기준이 아닌 thread(TID) 기준으로 CPU 점유

<img src="./img/thread.png" width="400px"><br>
&rarr; 공통 자원 : code, data, files  
&rarr; 별도 자원 : register, stack, PC(program counter)  

#### multithreading 흐름
<img src="./img/Multithread_server_architecture.png" width="400px"><br>
1) client가 server에게 request 진행
2) thread에게 작업을 맡기고
3) server는 Nonblocking으로 계속 resume 가능  
&rarr; 계속 요청이 들어와도 Thread 생성 할 수 있는 한도까지는 계속 생성하여 사용  

#### multithreading 장점
• <b>Responsiveness</b>: may allow continued execution (Nonblocking으로 계속 execution 가능)  
&rarr; if part of process is blocked, especially important for user interfaces.  
• <b>Resource Sharing</b>: threads share resources of process(별도로 지정해 주지 않아도 code, data, files를 shared-memory처럼 사용 가능)  
&rarr; easier than shared-memory or message-passing(shared-memory, message-passing는 process 단위의 데이터 통신 방식)  
• <b>Economy</b>: cheaper than process creation,
&rarr; thread switching lower overhead than context switching.
• <b>Scalability(확장성)</b>: process can take advantage of multiprocessor architectures

## Thread Library

### Threads in Java
• In a Java program,  
- threads are the fundamental model of program execution.  
• Java provides a rich set of features   
- for the creation and management of threads  

▪ Three techniques for explicitly creating threads in Java.  
• Inheritance from the Thread class  
- create a new class that is derived from the Thread class.  
- and override its public void run() method.  
• Implementing the Runnable interface.  
- define a new class that implements the Runnable interface.  
- and override its public void run() method.  
• Using the Lambda expression (beginning with Java Version 1.8)  
- rather than defining a new class,   
- use a lambda expression of Runnable instead.  
 
▪ 방법 1: Thread 클래스 상속받기  
```java
class MyThread1 extends Thread {
  public void run() {
    try {
      while (true) {
        System.out.println("Hello, Thread!");
        Thread.sleep(500);
      }
    }
    catch (InterruptedException ie) {
      System.out.println("I'm interrupted");
    }
  }
}
```