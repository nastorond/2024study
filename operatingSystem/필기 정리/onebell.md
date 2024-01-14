# 운영체제

## Chapter 1

### 운영체제란?
- Operating System
- **컴퓨터 시스템을 운영하는 소프트웨어**
- windows10, linux, mac OS X

1. H/W 디바이스 디바이스들을 제어
2. 운영체제를 통해 App들을 이용할 수 있도록 인터페이스를 저장
3. 유저가 OS를 통해 저장된 인터페이스를 사용할 때 I/O 관리 

-> H/W, S/W, USER 간의 중간 매개 역할을 함

#### 운영체제도 프로그램?
- O. OS는 컴퓨터에서 항상 실행되는 프로그램이다.
- 시스템 서비스를 애플리케이션에게 지정해줌
- **프로세스**(굉장히 중요!!), 리소스, UI,  를 관리하는 역할

### 컴퓨터란?
- 스마트폰은 컴퓨터인가? -> O
- 계산기는 컴퓨터인가? -> X

- 컴퓨터는 정보를 처리하는 기계이다. 

#### 정보란?
- 어떤 불확실한 상황을 측정해서 수치적으로 표현한 것
- I(x) = -log2P(x)
- 어떤 사건 x가 일어났을 때 사건 x의 정보량 = 사건이 발생한 확률에 -log를 붙인 것

- 정보의 최소 단위 = 1 bit (binary digit의 약자)
- 예시) 정보의 량이 8bit라면 이것은 1byte 이기도 하다.

- 정보의 처리 : 정보의 상태변환 (0에서 1로, 1에서 0으로)
- 부울 대수(Boolean Algebra) : NOT, AND, OR
- 논리 게이트 : NOT, AND, OR, XOR, NAND, NOR
- 논리 회로 : IC, LSI, VLSI, ULSI, SoC, ...
    - 무어의 법칙, 황의 법칙
- 정보의 저장과 전송 : 플립-플롭, 데이터 버스

#### 컴퓨터가 정보를 처리하는 법
- 덧셈은? 반가산기, 전가산기
- 뺄셈은? 2의 보수 표현법
- 곱셈과 나눗셈은? 덧셈과 뺄셈의 반복
- 실수 연산은? 부동 소수점 표현법
- 함수는? GOTO
- 삼각함수, 미분, 적분, 사진 촬영, 동영상 재생, ...

#### 컴퓨터가 만능?
- 범용성 : universality
    - NOT, AND, OR 게이트만으로 모든 계산을 할 수 있다.
    - NAND 게이트만으로 모든 계산을 할 수 있다.
    - 범용 컴퓨터 : general-purpose computer

- 계산가능성 : computability
    - Turing-computable : 튜링 머신으로 계산가능한 것
    - 정지 문제 (Halting Problem): 튜링 머신으로 풀 수 없는 문제

#### 컴퓨터를 만든 사람?
- 컴퓨터의 할아버지
    - Alan Turing - Turing Machine(1936)
    - Head, Tape, Turing Machines, **Universal Turing Machine**
    - CPU, RAM, Application Programs, **Operation System**
    - Turing Machine : 주니온TV <TMItalk: 튜링 머신의 이해> 참조

- 컴퓨터의 아버지
    - John von Neumann - ISA(Instruction Set Architecture)
    - 내장형 프로그램 방식 : 메모리에 프로그램을 저장하는 컴퓨터

#### 프로그램이란?
- 컴퓨터 하드웨어에게 할 일을 실행시키는 명령어들의 집합

## Chapter 2
### 운영체제가 하는 일
- 일반적인 정의 : 컴퓨터에서 항상 실행되고 있는 프로그램
- 보통 **"kernel(커널)"** 이라고 부른다.
- kernal은 OS의 핵심을 담당하고 다음 2가지 프로그램에 대한 인터페이스를 제공해 줌
    - system programs
    - application programs

### Bootstrap program
- 부팅하기 위한 프로그램
- 운영체제를 메모리에 로딩하는 역할

- 메모리의 ROM에 저장된 전원을 키기위한 프로그램을 'bootstrap' 이라 한다.

### Interrupts
- CPU와 I/O device(주변 장치)가 통신하는 방법
- 하드웨어는 interrupt를 언제든지 trigger 시켜줄 수 있고 trigger를 시키면 signal을 CPU로 보내줌
- 그러면 system bus를 통해서 signal을 전송해줌
- ex) 키보드를 누르면 interrupt가 발생하고 trigger 시켜 signal이 전송되어 인식됨

### von Neumann architecture (폰 노이만 아키텍처)
- fetches하고 executed하는 사이클을 폰 노이만 아키텍처 라고 한다.
- instruction register - instruction을 메모리에서 하나씩 가져오는 register
- 자세한 건 프로세스에서 다룰 예정

### CS Organization (계층구조)
빠른 순서와 저장 공간에 따라 정렬
1. registers - 굉장히 빠름
2. cache - ram보다는 작지만 빠른 cache, ram보다는 비쌈
3. main memory - 주 기억장치
4. solid-state disk(SSD) - 메모리 형태의 하드디스크 역할
5. hard disk(HDD) - 마그네틱 카드 자기장을 이용한 하드디스크
6. optical disk - HDD 용량마저 부족할 시 사용
7. magnetic tapes - backup을 위한 용도로 보존을 위해 사용

### I/O structure
- 어떤 CPU가 thread of execution을 가지고 있는데 cache 메모리를 통해서 이 RAM에 access를 하고 어 그다음에 어 I/O device가 interrupt를 걸고 뭐 데이터를 주고 받고 뭐 I/O request를 보내고 또 interrupt 걸고 데이터 주고 받고~ (?)

### Multiprogramming
- 여러 개의 프로그램을 동시에 실행되는 것
- 메모리에 여러 개의 프로세스가 동시에 올라가있어 동시에 실행 시킴

### Multitasking(=multiprocessing)
- Multiprogramming이 되기 때문에 가능해짐
- CPU가 가능한 일의 양 범위에서 time 시간 축에 따라 time-sharing을 하며 여러 개의 작업이 가능해지는 것
- concurrency 과 parallelism (아 스펠링 틀리는거 불편하네;) -> 3장에서 다시 배움 

#### CPU scheduling
- 5장에서 다시 다룸
- CPU 효율을 제일 좋게 만들기 위해 어떤 process를 선택할 지 선택방법을 찾는 것
- RAM에 존재하는 프로그램 중에서 CPU가 실행시켰다가 다시 종료시켰을 때 다시
실행 시키는 program을 찾음. 이 때 효율이 제일 좋은 방법을 찾기 위해 CPU scheduling을 함

### OS Operations
- 운영체제에 있는 2가지의 mode
    - user mode
    - kernel mode

- 다른 프로그램이 방해되게 하는 것을 막아주는 것도 운영체제가 하는 일이다.
- user process에서 system call을 하면 kernal mode로 바꿔서 system call을 처리하고 다시 user mode로 되돌림


#### 사용자가 OS에 접근
- CLI(Command Line Interface)
    - 흔히 shell이라 불리는 개념
    - sh, bash, csh, tcsh, zsh 등
    - command(명령어)를 기반으로 interface하는 것 
- GUI(Graphical User Interface)
    - 마우스로 아이콘 클릭하는 것 등
    - Windows, Aqua for MacOS, KDE/GNOME for Linux 등
- Touch-Screen Interface
    - Android UI, iPhone UI 등

#### 컴퓨터가 OS에 접근
##### System Calls
- OS가 제공해 주는 서비스들을 system call을 통해 호출 
- 대표적으로
    - #include <unistd.h>
    - ssize_t **read** (int **fd**, void ***buf**, size_t count)

    -> fd(file description)의 어떤 장치에 buf의 data를 count만큼 read하라는 뜻
    그 내용이 unity standard. header에 정의되어 있으면 함수를 호출

##### API
- Application Programming Interface
- OS의 system call = API

### Virtualization(가상화 기술)
- 하나의 컴퓨터가 있을 때 process를 multitasking을 이용해 동시 실행 시킬 수 있음

- 이와 같이 H/W에서 여러 개의 운영체제를 동시에 돌리고자 함.
- 이를 위해 필요 한 것이 VMM

#### VMM(Virtual Machine Manager)
- VMM을 이용해 H/W에서 복수의 O/S를 돌리고자 함.
- VMware, XEN, WSL 등이 있음.

### Computing Environments
- Traditional Computing 
    - 최근엔 거의 없음
- Mobile Computing 
    - 대부분의 핸드폰에서 사용
    - Android, iPhone 등등
- Client-Server Computing 
    - Web
    - 1:1 통신
- Peer-to-Peer Computing(P2P)
    - N:N 통신
    - 음악 파일 공유, 영화 공유 등
    - 파일을 공유할 때 굳이 server를 거치지 않고 파일을 서로 공유
    - P2P Network에 원장을 쓴 것이(?) Bitcoin
    - peer끼리 서로 직접 연결되어 통신
    - webRTC도 이거랑 같은건가?
- Cloud Computing
    - server안에 모든 컴퓨터 자원들이 구름처럼 가려져 있고 우리는 terminal을 통해 사용하는 개념이 cloud computing이다.
    - 서비스 할 때 웹서버 직접 구축하지 않고 cloud에서 가져다 사용
    - AWS, Azure, GCP, Naver, KAKAO 클라우드 등
    - 실제로 내가 사용하는 컴퓨터 자원은 네트워크를 통해 제공되는 UI이고 실질적인 저장은 cloud에 되어있음.

- Real-Time Embedded Systems(RTOS)
    - 화성 탐사 로봇
    - Vxworks 등
