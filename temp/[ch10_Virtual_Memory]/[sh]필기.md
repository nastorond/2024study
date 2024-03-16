# 가상 메모리와 디맨드 페이징
## Background
### _Virtual Memory
- a technique to allow the execution of processes
    - that are not completely in memory
    - so, programs can be larger than physical memory
- abstracts main memory into an extremely large array of storage
    - separating logical memory from physical memory
- provides an efficient mechanism
    - for sharing files and libraries and process creation<br>
<img src="./img/diagram_showing_virtual_memory.png" width="70%"><br>

- 그때 그때 필요할 때 마다 매핑해서 실행
### _Virtual Address Space_
- the logical(or virtual) view of how a process is stored in memory
- Typically, begins a certain logical address, to say, address 0,
- and exists in contiguous memory
### Virtual Memory
- allows _files_ and _memory_ to be shared by two or more processes
    - through _page sharing_<br>
<img src="./img/shared_lib_using_vr_memory.png" width="70%"><br>

## Demand Paging
### Consider how an executable program
- might be loaded from secondary storage into memory
    - ssd or hdd 로부터 로딩
- One option is to load the entire program in physical memory
    - 요즘엔 안함, 아두이노 같은건 아직 함 교육용이니까
- The _demand paging_ is an alternative strategy
    - to load pages only as they are needed
    - commonly used in virtual memory systems
- With _demand-paged virtual memory_,
    - pages are loaded only when they are _demanded_ during execution

### Basic Concepts of **the Demand Paging**
- While a process is executing
    - some pages will be _in memory_ and some will be _in secondary storage_
- To _distinguish_ between two situations
    - the _valid-invalid bit_ scheme can be used
    - _valid_ : the page is both legal and in memory
    - _invalid_ : the either is not valid or currently in secondary storage
    - dirty bit : 위 두개를 구분<br>
<img src="./img/page_table_when_some_pages_are_not_in_mainmemory.png" width="60%"><br>

- invalid 비트를 가지고 나중에 실제로 로딩되어있는지 판단, 안되어있으면 swapping

### The procedure for handling the _Page Fault_
1. _Check an internal table_ for the process to determine
    - whether the reference was _valid_ or _invalid_ memory access
2. If the reference was valid, terminate the process
    - or valid but page fault, we now _page_ it _in_
3. Find a _free frame_(by taking one from the free-frame list)
4. Schedule a secondary storage operation
    - to _read the desired page_ into the newly allocated frame
5. When the storage read is complete, _modify the internal table and_
    - _the page table_ to indicate that the page is now in memory
6. _Restart the insturction_ that was interrupted by the trap<br>
<img src="./img/steps_in_handling_apage_fault.png" width="60%"><br>

### _Pure_ Demand Paging
- _never bring a page_ into memory _until it is required_
- With the scheme of pure demanding paging
    - we can start executing a process with _no pages_ in memory
- 진짜 백지로 만들고, 필요할 때 마다 꺼내와서 사용하는 것.
- When the OS sets the instruction pointer
    - to the _first instruction_ of the process with a _page fault_
    - the page of the process would be paged in
### _Locality of Reference_ 참조 국부성
- If a program accesses _several new pages_ with _each insturction_
    - to say, _one page_ for the _instruction_ and _many pages for data_
    - possibly causes _multiple page faults per instruction_
- Fortunately, analysis running processes
    - show that this behavior is exceedingly unlikely
- Programs tend to have _the locality of reference_,
    - which results in _reasonable performance_ from _demand paging_
### An e.g. of Program Structure
- Let the _page size_ to be 128 and an _array_ to be $128$ x $128$
    - then compare the following tow codes
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
    } // 이미 기로딩 되어있는 페이지를 사용하기 때문에 위 코드보다 이론적으로 뛰어남
    // 1번이라는데 아마 128번일거고 위에는 이거에 제곱일듯 아님말고
    ```
- Careful selection of data structures and programming structures
    - can increase _the locality of code or data_
    - hence, _lower the page-fault_ rate and _enhance the system performance_
    - 특정한 부분만 엑섹스를 계속 함, 공평하게 엑세스 하는게 아님
### Hw Support to Demand Paging
- _Page talbe_ :
    - has the ability to mark valid or invalid. (with a valid-invalid bit)
- _Secondary memory_(=_swap space_) :
    - holds thoses pages that are not present in main memory
    - usually a high-speed disk or NVM device
    - 운영체제에서 속도가 느려지면 swap space를 늘려라 라는데,,
    - swap in-out 하는 영역을 늘려라
    - 그래서 ssd 쓰면 빠른거였누, swap이 빨라지니까 lane swap
### Insturction Restart
- A crucial requirement for demand paging
    - the ability to restart any instruction after page fault
- When the page fault occurs,
    - the state of interrupted process (registers, condition code, instruction counter, etc.) is saved
    - 또 또 waiting queue 너야
    - 문 맥 교 환
- Therefore, _restart_ the process in _exactly the same place and state_
    - 프로세스 별로 페이지 테이블을 잘 관리 해야함
- If a _page fault_ occurs _on the instruction fetch_,
    - restart by fetching the insturction again
- If a _page fault_ occurs while we are _fetching an operand_,
    - fetch and decode the instruction again and then fetch the operand
### As a worst-case ex
- ADD A, B, C; three address insturction, adding A and B into C. 어셈블리어 명령어임
    1. Fetch and decode the instruction(ADD)
    2. Fetch A
    3. Fetch B
    4. ADD A and B
    5. Store the sum in C
    - 어디서 아웃 되든 첨부터
### Free Frame List
- Whem a page fault occurs
    - OS must bring the desired page from secondary storage into memory
- To resolve page faults, OS maintains the _free frame list_:
    - a pool of free frames for satisfying such requests
- Free frames must also be allocated
    - when the stack or heap segments from a process expand<br>
<img src="./img/list_of_freeframes.png" width="30%"><br>

### Performance of Demand Paging
- How to compute the _effective access time_ for a _demand-paged_ memory?
- Let _ma_ to denote the _memory-access time_
- Let _p_ be the _probability_ of a _page fault_
    - EAT = $(1-p)$ x $ma + p * (page-fault-time) $
- How much time is needed to service a page fault?
    - three major activities
    - Service the page-fault interrupt
    - Read in the page 얘가 젤 큼
    - Restart the process
### Consider a system
- with an average page-fault service time of _8 milliseconds_
    - and a memory access time of _200 nanoseconds_,
    - EAT = $(1-p)$ x $200 + p * 8,000,000 $<br>
          = $200 + 7,999,800$ x $p$
    - If one access out of 1,000 causes a page fault $(p = 0.001)$,
        - EAT = $200 + 7999.8 = 8199.8$ *nanoseconds* $\simeq 8.2$ *microseconds*

## Copy-on-Write
### Copy-on-Write:
- _copy_ a shared page _only when_ a process _writes_ to a shared page
- Recall the process creation with **fork()** and **exec()**<br>
<img src="./img/copy_on_write.png" width="70%"><br>

