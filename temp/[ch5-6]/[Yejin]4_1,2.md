## Thread
• a lightweight process.
• a basic unit of CPU utilization.
• comprises a thread ID, a program counter, a register set, and a stack.

▪ Motivation for multithreading
• Let us consider the case of client-server system, e.g., a web server.

▪ The benefits of multithreaded programming:
• Responsiveness: may allow continued execution 
- if part of process is blocked, especially important for user interfaces.
• Resource Sharing: threads share resources of process,
- easier than shared-memory or message-passing.
• Economy: cheaper than process creation,
- thread switching lower overhead than context switching.
• Scalability: process can take advantage of multiprocessor architectures

▪ Threads in Java
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