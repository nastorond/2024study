# Chapter11~15. 스토리지와 입출력 (Storage Management)
## Mass-Storage Structure
### Mass-Storage
- 비휘발성 메모리 / 세컨더리 메모리 시스템
- 일반적으로 HDD, NVM 등을 얘기함
- 백업용으로 마그네틱 테잎을 쓰는곳도 많고 광학 디스크를 쓰는데도 있고 클라우드 스토리지를 이용하는 곳도 있음
- 2차 저장 장치에는 RAID system이라는 걸 적용함

#### RAID system (Redundant Array of Independent/Inexpensive Disk )
- 독립된/ 저렴한 디스크의 복수 배열
  - 디스크 어레이(disk array)라고도 한다
  - 여러 개의 디스크를 묶어 하나의 디스크 처럼 사용하는 기술
- 기대 효과
  - 대용량의 단일 볼륨을 사용하는 효과
  - 디스크 I/O 병렬화로 인한 성능 향상 (RAID 0, RAID 5, RAID 6 등)
  - 데이터 복제로 인한 안정성 향상 (RAID 1 등)
- RAID Level
  - RAID 0 ~ RAID 6 까지 존재
  - 최근 출시되는 RAID 컨트롤러에서 사용 가능 한 RAID Level은 RAID 0, RAID 1, RAID 5, RAID 6 까지임
  - 자세한 RAID Level 설명은 [여기](https://devocean.sk.com/blog/techBoardDetail.do?ID=163608)에서 참고

### Hard Disk Drives
<img src="./img/HDD.png" width="60%">

- 하드디스크를 뜯어본 모습
- spindle이 돌아가는 rotation값인 RPM에 따라 성능 차이 구분
- 대충 HDD 설명하는 내용...

### HDD Scheduling
- scheduling의 목표
  - access time / seek time 줄이기
  - 데이터 전송의 bandwidth를 최소화 하기

#### seek time
- 이걸 줄이는 게 가장 큰 목표
- device arm이 head를 움직이는데 특정 cylinder의 특정 sector를 찾아가는데 걸리는 시간
  - rotational latency (읽고자 하는 sector에 가는 시간)가 데이터를 읽고 쓸 때 시간이 많이 걸림
#### disk bandwidth
- 한 번에 transfer 할수 있는 용량 $\div$ 전체 시간
  - sector가 크면 용량이 커짐
  - bandwidth를 늘려줘야 함

### FIFO Scheduling
<img src="./img/FCFS_disk_scheduling.png" width="60%">

- 맨날하니까 깊이 설명 안함
- 당연하게도 최적은 아님
- cylinder를 찾기 위해서 head movement가 일어나야 하는가
  - 그림에서는 총 길이 640 cylinder가 나옴

### SCAN Scheduling
- 비효율적으로 왔다갔다 하지말고 그냥 scan을 해버리면 되지 않는가
  - disk arm이 한 쪽 end에서부터 다른쪽 end로 계속 움직이면서 scan
  - 움직이면서 request들을 처리주면 주면 되지 않는가
  - 현재 들어온 request들을 쭉 처리해 주면서 왔다갔다 하자
- scan이 일어나는 방향이 중요함
  <img src="./img/SCAN_disk_scheduling.png" width="60%">
  - 만약 그림에서 head가 0을 향한다면 그림과 같이 움직이면 queue값을 다 처리 가능함
  - 다 처리했을 때 cylinder 길이는 236이 나옴
  - 근데 생각해봤을 때 양쪽 왔다갔다 만드는거 보다는 한 방향으로 쭉 가는게 만들기 쉬움
  - 그래서 C-SCAN이라는 걸 도입

### C-SCAN(Circular-SCAN) Scheduling
- scan을 할 때 좀 더 uniform한 wait time을 주자 (= 한 방향으로만 헤드를 움직이자)
- 한 방향으로 갈 때 scan을 하고 돌아올때는 scan 하지말고 그냥 오자
  - 장치를 오래 쓸 수 있다는 장점도 생김
- 이렇게 하면 일종의 circular list가 됨

<img src="./img/C-SCAN_disk_scheduling.png" width="60%">

- 어디서 시작하든 한 쪽 방향으로 쭉 scan
- 끝에가면 0으로 확 돌아와버리기
- 총 183 cylinder가 나옴 (0으로 갈때는 무시)
  - 굉장히 효율적
- Loop, C-Loop라는 것도 있는데 안중요하니까 빼겠죠?

### Boot Block
<img src="./img/boot_block.png" width="60%">

- mass storage를 쓸 때 고려해줘야 하는 사항
- 얘를 다시 다루는 이유
  - flash memory인 ROM에 저장해줘야 함
    - 처음 컴퓨터를 실행시킬 때 initial program인 bootstrapping
    - 운영체제 kernal을 load해줌
    - 그러고 운영체제한테 제어권을 넘겨줌
- 얘도 storage, second storage의 일종이다.

### RAID
- 위에 정리해놨는데 여기서 나오네;
- 데이터의 저장과 읽기, 쓰기 bandwidth를 늘려줘야 하는데 이걸 parallel하게 할 수 있음
