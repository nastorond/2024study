## 동시성(Concurrency)과 병렬성(Parallelism)의 차이
### Concurrency
javascript의 thread : single-thread 지원
즉, 하나의 thread에서 번갈아가며 처리

### Parallelism
java의 thread : multi-thread 지원
즉, 여러 thread에서 각각 처리

## 왜 원하는 값이 나오지 않는가?
한 문장으로 생각되었던 count++, count--은 사실 3문장으로 나뉘어 있음
```c
register = count
register = register + 1
count = register

register = count
register = register - 1
count = register
```

따라서 context switch가 발생할 수 있는 순간은 3군데 모두  
처음이나, 마지막에 발생하면 문제가 없지만,  
중간에 진행되면 문제 발생  
즉, instruction의 어디에서 context switch가 발생하느냐에 따라 save, restore 시 문제 발생 가능  

### 경쟁 상황
순서에 따라 결과가 달라지는 문제점   
순차적 실행되도록 동기화 진행  
Q. 그럼 번갈아 진행 안하면 concurrency가 없어지는 것 아닌가?  
Q. static으로 변수 선언 시 class 변수가 된다?  
-> 공유된 변수이기 때문에  
-> 따라서 문제 발생  
Q. 그럼 static이 아니면 따로 count가 각각 존재하는거고, static이면 두 개에서 하나의 변수를 공유하는 것인가?  

### 임계 영역
critical section : 다른 프로세스와 공유되고 있을 때  
진행 되고 있을 때 진입을 막자  
Q. 그러면!! concurrency가 없어지는거 아니냐니까?  
동기적이면 비동기로 일어나지 못하니까 안되는거 아닌가?  

