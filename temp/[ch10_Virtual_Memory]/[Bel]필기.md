# Chapter10. 가상 메모리와 디맨드 페이징
## Background
### Virtual Memory
- process의 execution을 memory에 다 올리지 않아도 (실제로 physical보다 커도) 실행할 수 있도록 해줌
- 말 그대로 가상의 메모리로 logical과 physical을 완전히 분리할 수 있다.
- 파일, 라이브러리 프로세스를 공유할 때 유용하게 사용 <br>
<img src="./img/virtual_memory2.png" width="50%">

  - 각 공유하는 process가 shared library의 내용을 shared pages에 저장
  - heap ~ text까지가 접근할 때 문제가 없어짐
  - 뒤에 [demand paging](#demand-paging)까지 배우고 나면 더 잘 이해가 될듯 <br>
<img src="./img/virtual_memory.png" width="50%">

### Virtual Address Space
- logical한 process의 memory 구조

<img src="./img/virtual_address_space.png" width="50%">

- 다음 storage할 때 다시 다룰 거
- contiguous 한 memory를 다룰 때 아주 편리해 질 것

## Demand Paging
### Consider how an executable program
- SSD나 HDD같은 secondary memory에서 loading하는 작업을 먼저 해줘야 함. 
- 한꺼번에 다 loading할 필요없음(현대적인 거에서는 안함)
  - 라즈베리파이나 아두이노 같은 원시적인 거에서는 함.
  - 실제 응용하는 거에서는 다 할 필요가 없음
- 각 memory를 잘라놓은 것들을 언제 올릴거냐
  - 저번에 배운 paging의 개념에서 더 나아가 demand paging이라는 개념이 필요
  - 요청할 때만 올려주자
  - 이렇게 하면 많은 문제가 생김
  - 유용하긴 하지만 고려할게 많아서

### Basic Concepts of the Demand Paging
- 어떤 프로세스가 실행 중 = page가 memory에 있음
- memory에 있음 / secondary memory에 있음
- 표시해 줘야하는데 H/W의 지원이 필요함
  - 저번에 배웠던 valid-invalid bit
  - valid
    - page가 memory에 있음
  - invalid
    - page가 없음
    - page가 secondary memory에 있음
  - dirty bit
<img src="./img/demand_page_table.png" width="60%">

- 위 사진에서 logical memory에서 loading했는데 invalid할 때
  - physical memory에 할당이 안되어 있음
  - page fault가 일어남
  - backing store에 있는 것과 swap 해줘야 함.

### The procedure for handling the Page Fault
- memory loading이 안된 page에 access(page fault)했을 때 어떻게 처리해 줄까
1. internal page 확인 후 valid인지 invalid인지 확인
2. valid면 처리해 주면 되고 page fault면 page in 해줘야 함.
3. free frame에다가 H/W 에 있는 page 영역을 page in 해줌
  - free frame list를 OS가 관리해주고 있어야 댐
4. second storage에 가서 page를 읽어서 새로운 frame에 할당해 줌.
5. 이후에 invalid 를 load했다고 처리해줌( valid로 바꿔줌)
6. 다시 instruction을 실행
<img src="./img/handling_page_fault.png" width="60%">

- 이거 시험에 자주 나옴

### Pure Demand Paging
- 딱 요청할 때만 page를 가져오는 것
- 처음에 하나의 page도 loading할 필요가 없음
- 그때그때 page를 loading해야 하기 때문에 나쁨
  - page를 여러 개 loading할 때 page 하나하나마다 page fault가 일어나서
  - 시간이 3분의 1...?
  - 3배를 잘못 말한게 아닌가

### Locality of Reference
참조 국부성 = 참조 지역성

### An example of Program Structure
```c
int i, j;
int[128][128] data;
for (j=0; j<128; j++) {
    for (i=0; i<128; i++) {
        data[i][j] = 0;
    }
} // 계속 페이지 fault 남
```
```c
int i, j;
int[128][128] data;
for (i=0; i<128; i++) {
    for (j=0; j<128; j++) {
        data[i][j] = 0;
    }
}
```
