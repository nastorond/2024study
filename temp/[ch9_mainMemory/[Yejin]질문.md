# 주메모리의 관리
프로세스 : 메인 메모리에 프로그램이 로드되어 있는 상태

segmentation fault : 범위 이탈
https://ko.wikipedia.org/wiki/%EC%84%B8%EA%B7%B8%EB%A9%98%ED%85%8C%EC%9D%B4%EC%85%98_%EC%98%A4%EB%A5%98

source - 컴파일러가 메모리 공간 지정 (컴파일러가 binary 파일 생성)



int a;  
a = 10; 인 경우  
int a 라는 변수가 메모리 공간 확보  
컴파일러가   
    - 바이너리 파일 생성
    - 메모리 주소 공간 지정
a : symbolic but, 주소는 모름(컴파일러가 알고있음)  
컴파일러가 만든 exe 파일도 결국 바이너리 파일로 disk에 저장되어 있음  
-> 프로그램 일뿐  
실행(메모리에 가지고 오면)  -> 프로세서  
프로세서 주소는 O/S 커널이 결정 
그때그때 달라짐
***Q. 컴파일러가 주소 지정해준다는거 아니였나? O/S 커널이 결정하나?***
*** 아 COMPILER는 RELOCATION 주소 지정해주고 커널...이? LINKER? LOADER? ***

## paging
### shared pages
***libc가 뭐지?***
몰루 그냥 다같이 쓰는거~