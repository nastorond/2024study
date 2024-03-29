# 13 뮤텍스와 세마포어
## Mutex locks
### Higher-level software tools to solve the CPS:
- soft : Peterson
- hard : Atomic variable
    - 둘 다 실전에서는 사용하기 어려움
- _Mutex Locks_ : the simplest tools for synchronization
    - 임계영역에 접근하려면 열쇠가 필요하게 만들어버림
    - 두개만 있다고 생각했을 때 가장 간단
- _Semaphore_ : more robust, convenient, and effective tool
- _Monitor_: overcomes the demerits of mutex and semaphore
    - 요즘에 다 이거 씀
    - 위 모두 상호 배제 문제만 해결해줌.
- _Liveness_ : ensures for processes to make progress
    - 얘는 deadlock 까지 해결
### Mutex Lock
- *mutex* : **mu**tal **ex**clusion
- to protect critical section and prevent race condition
- a progress must _acquire_ teh ***lock*** before _entering_ a critical section
- _release_ the ***lock*** when it exits the critical section
### Two functions and one variable for the Mutex Locks:
- acquire() and release()
- availabe : a Bollean variable whose value indicates 
    - if the lock is available or not
### The definition of acquire() and release():<br>
<img src="./img/define_acquire_release.png" width="70%"><br>

- 다시 3번
- Calls to either acquire() and release() must be preformed _atomically_
- can be implemented using the *compare_and_swap* operation

### Busy waiting : 바쁜대기
- Any other process trying to enter its critical section
    - must _loop continuously_ in the call to acquire()
- busy waiting is clearly a _problem_ in a real multiprogramming system,
    - where a single CPU core is shared among many processes
    - _waste CPU cycles_ for some other processes to use productively
- 이걸 막는게 가장 중요함
### Spinlock: 
- the type of mutex lock using the method of _busy waiting_
- the process _spins_ while waiting for the lock become available
- However, spinlocks do have an _advantage_
    - in that _no context switch_ is required waiting on a lock
    - a context switch may take considerable time
- busy waiting 하고 있으면 context switch 가 안일어나서 그거 쓰는 시간이 걸림
- ready queue 갈 시간을 아낄 수 있다
- 여러개의 CPU를 사용하고 있을 경우에
- Incertain circumstances _on multicore systems_
    - spinlocks are the _preferable_ choice for locking
    - One thread can _spin_ on one _processing core_
    - while another thread performs its critical section _on another core_
- 구현<br>
<img src="./img/how_to_use_mutex_lock.png" width="70%"><br>

- lock &rarr; onlock 의 구조를 반복
- 간단한건 이걸로 컷 / 개잘되네 ㄷ

## Semaphores
### Semaphore
- 의미 : **신호장치, 신호기**
- e.g. 간단한 예제<br>
<img src="./img/eg_semaphore.png" width="70%"><br>

### Defining the Semaphore
- A _semaphore_ S is
    - an integer variable that, apart from initialization
    - is accessed only through _two standard atomic operations_
    - **wait()** and **signal()**, or sometimes **P()** and **V()**
- P() and V() are introduced by Edsger Dijkstra(독일사람임)
    - Proberen(to test) and Verhogen(to increment) 아마 독일어인듯
### Definition of wait() and signal()
<img src="./img/Definition_of_wait()_and_signal().png" width="50%"><br>

- All _modifications_ to the integer value of the semaphore
    - in the wait() and signal() operations must be executed _atomically_
- S가 10일때 감소시키는걸로 해야겠네 머임
- 열쇠가 한개가 아니라 꾸러미가 있는거임
    - 열쇠 락커가 여러개 있고 맞는데 가져다 쓰는 느낌
    - 다쓰면 이제 진입못하게 하고 밖에서 대기하게 함
    - 비선점형 방식이랑 비슷한듯?
### Binary and Counting Semaphores
- n 개의 인스턴스를 가진 자원을 공유할 때 사용 가능
- _Binary_ semaphore
    - range only between 0 and 1 : similar to _mutex_lock_
- _Counting_ Semaphore
    - range over an unrestricted domain
    - can be used to resources with _a finite number of instances_
### Using the counting semaphore
- _Initialize_ a semaphore to _the number of resources available_
- When a process uses a reousrce
    - _wait()_ on the semaphore:  _decrement_ the count
- When a process release a resource
    - _signal()_ on the semaphore: _increment_ the ount
- When the count goes to 0, all resources are being used
    - The, processes that wish to use a resource _will block_
    - _unitl_ the count becomes _greater than 0_
### Using the semaphore to solve synchronization problem
- Consider two processes $P_1$ and $P_2$ running concurrently
    - $P_1$ with a statement $S_1$, and $P_2$ with a statement $S_2$
- Suppose that $S_2$ should be _executed only after_ $S_1$ has completed
    - Let $P_1$ and $P_2$ share a _semaphore_ **synch**, initialized to 0.
    - 초기화를 0으로 해서 순서대로 진행될 수 밖에 없음
### Semaphore Implementation:
- Semaphores alos suffer from the problem of _busy waiting_
- To overcome this problem, modify the definition of P() and V()
- WHen a process executes the **wait()** operation
    - and finds that the semaphore _is not positive_, it must wait
    - rather tahn busy waiting, suspend itself and goes to the _waiting queue_
- When other process executes the **signal()** operation
    - waiting processes can be _restarted(wake up)_ and placed into the _ready queue_
- impl<br>
<img src="./img/impl_semaphores.png" width="70%"><br>
<img src="./img/impl_semaphores2.png" width="70%"><br>

- S = n 으로 구현 / Counting Semaphore<br>
    <img src="./img/impl_semaphores3.png" width="50%"><br>
    - 얘는 좀 망가지네 &rarr; 이게 정상임
    - 왜냐, 열쇠가 5개임 지금. 그래서 race condition이 그대로 발생 했음. 상호배제 실패함
    - n개의 instance 가 없어서 이런 게 발생
    - n을 S로 사용할 때, 인스턴스의 개수와 동일해야 함
    - n을 다시 1로 바꾸면 binary semaphore 가 되니까 정상적으로 작동함.
- 위 모든 건 locking 을 기반으로 구현

# 모니터와 자바 동기화
## Monitors
### The _difficulty_ of using semaphores
- The semaphore is _convenient_ and _Effective_ for synchronization
- However, _timing errors_ can happen
    - if particular execution sequences take place
    - these sequence _do not always occur_
    - and it is _hard to detect_
### An illustrative e.g. of semaphore's prob
- All processes share a _Binary semaphore_ mutex initialized to 1
    - Each process must **wait(mutex)** before entering the CS
    - and **signal(mutex)** afterward
- If this sequence is not observed
    - two processes _may be in_ their critical sections _simultaneously_
### Situation 1:
- Note that the difficulty arises
    - even if a signal process is not well behaved
- Suppose that a program _interchanges the order_
    - in which **wait()** and **signal()** on the semaphore **mutex** are executed
```c
signal(mutex);
    ...
    ciritcal section
    ...
wait(mutex);
```
### Situation 2 & 3
- Suppose that a program replaces _signal()_ with _wait()_
- Suppose that a process omitss the **wait()**, or the **signal()**, or both of them
    - 해줘야 하는 위치에서 안해주면
### How to deal with these kinds of difficulties?
- These situations ma y be caused
    - by an honest programming error or an uncooperating programmer
- Various types of errors can be generated easily
    - when programmers use semaphores (or mutex locks) incorrectly
- INcorporate simple synchronization tools
    - as high-level language constructs
    - _monitor_: one fundamental high-level synchronization struct
### A monitor type is
- an ADT that includes a set of _programer-defined operations_
    - that provided with mutual exclusion wiin the _monitor_
- declare the variables
    - whose values define the _state of an instatnce_ of that type
    - along with the bodies of _function_ that operate on those _variables_<br>
<img src="./img/pseducode_monitor.png" width="50%"><br>
<img src="./img/motitor_structure.png" height="30%"><br>

### Conditional Variables
- The monitor construct is not sufficiently powerful
    - for modeling some synchronization schemes
- We need to define the _condition_ construct
    - to provide additional synchronization mechanisms
### Using conditional variables:
```
condition x, y;
x.wait();
x.signal();
```
<br>
<img src="./img/monitor_with_x_y.png" height="30%"><br>

### Java Monitors
- Java provides a _monitor-like_
    - concurrency mechanism for thread synchronization
    - called as monitor-lock or intrinsic-lock
- Basic language constructs for Java Synchronization
    - ***synchronized*** keyword
    - ***wait()*** and ***notify()*** method

### synchronized keyword
- 임계영역에 해당하는 코드 블록을 선언할 떄 사용하는 자바 키워드
- 해당 코드 블럭(임계영역)에는 모니터락을 획득해야 진입 가능
- 모니터락을 가진 객체 인스턴스를 지정할 수 있음
- 메소드에 선언하면 메소드 코드 블록 전체가 임계영역으로 지정됨
    - 이때, 모니터락을 가진 객체 인스턴스는 this 객체 인스턴스임<br>
<img src="./img/synchronized_keyword.png" width="70%"><br>

### wait() and notify() methods:
- java.lang.Object 클래스에 선언됨 : 모든 자바 객체가 가진 메소드임
- 쓰레드가 어떤 객체의 wait()메소드를 호출하면
    - 해당 객체의 모니터락을 획득하기 위해 대기 상태로 진입함
- 쓰레드가 어떤 객체의 notify() 메소드를 호출하면
    - 해당 객체 모니터에 대기중인 쓰레드를 _하나_ 를 깨움
- notify() 대신에 notifyAll() 메소드를 호출하면
    - 해당 객체 모니터에 대기중인 쓰레드를 _전부_ 깨움
### Java Synchronization e.g. 1
<img src="./img/java_syc_monitor_ex.png" width="70%"><br>
<img src="./img/java_syc_monitor_ex2.png" width="70%"><br>

- 심각한 동기화 문제, race condition 발생
    - 모니터 락으로 해결
### Java Synchronization e.g. 2
<img src="./img/java_syc_monitor_ex_1.png" width="70%"><br>
<img src="./img/java_syc_monitor_ex2.png" width="70%"><br>

- 잘되네
- critical section만 synchronized 로 묶어 버림
- 다 해버리면 장점이 사라짐

### Java Synchronization e.g. 3
<img src="./img/java_syc_monitor_ex_3.png" width="70%"><br>
<img src="./img/java_syc_monitor_ex2.png" width="70%"><br>

### Java Synchronization e.g. 4
<img src="./img/java_syc_monitor_ex_4.png" width="70%"><br>
<img src="./img/java_syc_monitor_ex_4_2.png" width="70%"><br>

- counter 를 공유해야지만 동기화 문제가 발생하니까 static 변수로 사용
- 이건 동기화 문제 발생
- synchronized 블락은 this를 통해 자기참조를 함
    - 5개의 Thread는 각 instance를 가짐
- 객체가 달라지면 monitor가 달라지는 거임
- 자기 혼자만 동기화 되니까 동기화 안댐

### Java Synchronization e.g. 5
<img src="./img/java_syc_monitor_ex_4.png" width="70%"><br>
<img src="./img/java_syc_monitor_ex_5_2.png" width="70%"><br>

- this 를 모두 공유하게 만듬

## Liveness
### Liveness
- Two criteria for the CSP: the progress and bounded-waiting
    - Semaphores and monitors cannot solve these requirements
- _Liveness_ refers to
    - a set of properties that a sysytem must satisfy
    - to ensure that processes make progress during their execution cycle
- Two situations that can lead to liveness failures 
    - _deadlock_ and _priority inversion_
### Deadlock
- a situation where two or more processes are _Waiting indefinitely_
    - for an event that _can be caused only by_ one of the _waiting process_
    - e.g.<br>
        <img src="./img/ex_for_deadlock.png" width="40%"><br>
    - 8장가면 계속 나올거임
### Priority Inversion : 우선순위 역전
- A situation where a higher-priority processes have to wait
    - for a lower-priority one to finish ther resource
- It can arise when a _higher_-priority process
    - need to _read or modify kernel data_
    - that are currently being accessed by a _lowre-priority_ process
- Typically, priority inversion is avoided
    - by implementing a _priority-inheritance_ protocal
- All processes accessing resources needed by a higher-priority process
    - inherit the higher priority
    - until they release that resources



- liveness 정도가 있다 는 정도는 알아야 할듯