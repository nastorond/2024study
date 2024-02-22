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
