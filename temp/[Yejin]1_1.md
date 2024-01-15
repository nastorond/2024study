### 운영체제
컴퓨터 시스템을 운영하는 소프트웨어  
-> 컴퓨터 : 정보를 처리하는 기계  
-> 정보 : 불확실성을 측정하여 수치적으로 표현한 것
$$\ I(x) = - log_2 P(x)$$
-> # I : 정보량, P : 확률   
-> 정보 처리 방법  
    -> 정보의 최소 단위 : bit(binary digit)  
    -> 정보의 처리 : 정보의 상태 변환(0<->1)   
        -> 부울대수(Boolean Algebra) : NOT, AND, OR 
        -> 트랜지스터를 이용하여 게이트(스위치) 만들 수 있음
        -> 논리 게이트 : NOT, AND, OR, XOR, NAND, NOR  
        -> 논리 회로 : IC, LSI, VLSI, ULSI, SoC 등  
            - 무어의 법칙(chip의 집적도), 황의 법칙(메모리의 집적도)  
            - H/W의 경우 한계까지 발전해 왔음
        -> 정보의 저장과 전송 : 플립-플롭, 데이터버스

    -> 덧셈 : 반가산기, 전가산기
    -> 뺄셈 : 2의 보수 표현법
    -> 곱셈과 나눗셈 : 덧셈과 뺄셈의 반복
    -> 실수 연산 : 부동 소수점 표현법
    -> 함수 : GOTO
    -> 삼각함수, 미분, 적분, 사진촬영, 동영상 재생 등 가능

    -> 범용성(universality)
        -> NOT, AND, OR 게이트만으로도 모든 계산 가능
        -> NAND 게이트만으로 모든 계산 가능
        -> 범용 컴퓨터 : general-purpose computer
    -> 계산 가능성(computability)
        -> Turing-computable : 튜링 머신으로 계산 가능한 것
        -> 정지문제(Halting Problem) : 튜링 머신으로 계산 불가한 것
    
    -> 컴퓨터를 만든 사람
        -> Alan Turing - Turing Machine
            ![TuringMachine](./img/TuringMachine.PNG)
            Turning 컴퓨터 : Head, Tape, Turing Machines, Universal Turing Machine
            현대 컴퓨터 : CPU, RAM, Application Programs, Operating System

        -> John von Neumann - ISA(Instruction Set Architecture) : 실제 작동하는 컴퓨터
            내장형 프로그램을 처음 도입
            메모리에 프로그램을 저장하는 컴퓨터
            ![ISA](./img/ISA.PNG)
            RAM에서 정보를 가져와 CPU에 fetch하여 execute 진행


    -> 프로그램
        컴퓨터 하드웨어에 특정 task를 실행시키는 명령어들의 집합
        컴파일 후 기계어가 생김
        기계어의 경우 정보로 표현되어 있음
    
    -> 운영체제도 프로그램인가?
        Yes. 
        컴퓨터에서 항상 실행중인 프로그램
        하드웨어를 컨트롤
        시스템 서비스를 애플리케이션 프로그램에게 제공
        프로세스, 리소스(파일 등), 유저 인터페이스(마우스 등) 등을 관리

    -> 운영체제란?
        하드웨어를 제어하는, 사용자와 어플리케이션에 서비스를 제공하는 소프트웨어