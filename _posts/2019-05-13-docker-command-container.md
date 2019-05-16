---
title: "docker 기본 명령어:docker 컨테이너 조작"
date: 2019-05-13
categories:
  - docker
toc: true
---

## Docker 컨테이너 생성/시작/정지

### Docker 컨테이너의 라이프 사이클

* ![](/assets/images/container_life_cycle.PNG)
 
* **docker container create**  : 컨테이너 생성
  * 이미지로부터 컨테이너를 생성
  * 이미지 : 도커에서 서버 기능을 작동시키기 위해 필요한 디렉토리 및 파일들
  * 이미지에 포함될 Linux의 디렉토리와 파일들의 스냅샵을 취함

* **docker container run** : 컨테이너 생성 및 시작
  * linux의 프로세스 관리와 마찬가지로 nginx 등의 서버 프로세스를 백그라운드에서 항시 실행하거나 경우에 따라서는 강제 동료하는 일도 가능
  * 포트 번호와 같은 네트워크도 설정함으로써 외부에서 컨테이너의 프로세스에 엑세스할 수 있다.

* **docker container start** : 컨테이너 시작 (= docker start)

* **docker container stop** : 컨테이너 정지 (= docker stop)

* **docker container restart** : 컨테이너 재시작 (= docker restart)

* **docker container rm** : 컨테이너 삭제 (= docker rm)

* **docker container pause** : 컨테이너 일시 정지 (= docker pause)

### 컨테이너 생성 및 시작 (docker container run)

* `docker coniner run [옵션] 이미지명[:태그명][인수]`

* 옵션 | 설명
  ---- | ----
  --attach, -a | 표준 입력(STDIN), 표준 출력(STDOUT), 표준 오류 출력(STDERR)에 어태치한다.
  --cidfile | 컨테이너 ID를 파일로 출력.
  --detach, -d | 컨테이너를 생성하고 백그라운드에서 실행
  --interactive, -i | 컨테이너의 표준 입력을 연다.
  --tly, -t | 단말기 디바이스를 사용한다.

* `docker container run -it --name "test1" centos /bin/cal`
  * centos 이미지 바탕으로 test1이라는 이름의 컨테이너를 실행하고, 컨테이너 안에서 /bin/cal 명령을 실행
  * --name : 컨테이너 이름 설정. 생략할 경우 랜덤으로 자동 생성
  * -i : 컨테이너의 표준 입력(stdin)을 연다
  * -t : tty 모드(pseudo-TTY)를 사용
  

### 컨테이너의 백그라운드 실행 (docker container run)

* `docker container run [실행 옵션] 이미지명[:태그명] [인수]`

* 옵션 | 설명
  ---- | ----
  --detach, -d | 백드라운드에서 실행
  --user, -u | 사용자명을 지정
  --restart=[no &#124; on-failure &#124; on-failure:횟수n &#124; always &#124; unless-stopped] | 명령의 실행 결과에 따라 재시작을 하는 옵션
  --rm | 명령 실행 완료 후에 컨테이너를 자동으로 삭제
  
* `docker container run -d centos /bin/ping localhost`
  * -d : 컨테이너를 백그라운드에서 실행한다는것 (detach mode)
  * 위 명령어 결과는 콘솔에 표시되지 않는 대신 시작된 컨테이너의 컨테이너 ID가 표시됨
  
* *docker container logs* : 컨테이너 로그 확인
  * -t : 타임 스탬프 표시
  
* ```text
  PS C:\workspace> docker run -d ubuntu:14.04.5 /bin/ping localhost
  c8b79b1076ee9b775fbf6506001f7a99cfea90a26058a79e91a84732e30988d7
  PS C:\workspace> docker logs -t c8b79
  2019-05-14T02:14:33.922010500Z PING localhost (127.0.0.1) 56(84) bytes of data.
  2019-05-14T02:14:33.922054100Z 64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.083 ms
  2019-05-14T02:14:35.001595300Z 64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.024 ms
  2019-05-14T02:14:36.041728000Z 64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.066 ms
  2019-05-14T02:14:37.081595200Z 64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.045 ms
  2019-05-14T02:14:38.121446600Z 64 bytes from localhost (127.0.0.1): icmp_seq=5 ttl=64 time=0.027 ms
  2019-05-14T02:14:39.161812500Z 64 bytes from localhost (127.0.0.1): icmp_seq=6 ttl=64 time=0.050 ms
  2019-05-14T02:14:40.201652100Z 64 bytes from localhost (127.0.0.1): icmp_seq=7 ttl=64 time=0.046 ms
  2019-05-14T02:14:41.241663500Z 64 bytes from localhost (127.0.0.1): icmp_seq=8 ttl=64 time=0.046 ms
  2019-05-14T02:14:42.282166700Z 64 bytes from localhost (127.0.0.1): icmp_seq=9 ttl=64 time=0.092 ms
  ```

* --rm 옵션 : 실행 후의 컨테이너를 자동으로 삭제

* --restart : 명령의 실행 결과에 따라 컨테이너 재시작
  * 설정값 | 설명
    ---- | ----
    no | 재시작하지 않음
    on-failure | 종료 스테이터스가 0이 아닐 때 재시작
    on-failure:횟수n | 종료 스테이터스가 0이 아닐 때 n번 재시작한다.
    always | 항상 재시작
    unless-stopped | 최근 컨테이너가 정지 상태가 아니라면 항상 재시작

* --rm과 --restart 옵션은 동시에 사용할수 없다.




### 컨테이너의 네트워크 설정 (docker container run)

* `docker container run [네트워크 옵션] 이미지명[:태그명] [인수]`

* 옵션 | 설명
  ---- | ----
  --add-host=[호스트명:IP 주소] | 컨테이너의 /etc/hosts에 호스트명과 ip 주소를 정의
  --dns=[IP 주소] | 컨테이너용 DNS 서버의 IP 주소 지정
  --expose | 지정한 범위의 포트 번호를 할당
  --mac-address=[MAC 주소] | 컨테이너 MAC 주소를 지정
  --net=[bridge&#124;none&#124;container:>name&#124;id> &#124;host&#124;NETWORK]| 컨테이너의 네트워크를 지정
  --hostname, -h | 컨테이너 자신의 호스트 명을 정함
  --publish, -p[호스트의 포트 번호]:[컨테이너의 포트 번호] | 호스트와 컨테이너의 포트 매핑
  --publish-all, -p | 호스트의 임의의 포트를 컨테이너에 할당
 
* `docker container run -d -p 8080:80 nginx`
  * 백그라운드에서 nginx 실행. 이때 호스트의 포트 번호 8080과 컨테이너의 포트 번호 80을 매핑

* `docker container run -d --dns 192.168.1.1 nginx`
  * dns 서버 설정 
  
* `docker container run -d --mac-address="92.d0.c6.0a.29.33" centos`
  * mac 주소 설정
  
* `docker container run -it --add-host test.com:192.168.1.1 centos`
  * 컨테이너 안의 /etc/hosts에 호스트명과 IP 주소를 정의할수도 있다.
  
* --net 옵션 지정
  * 설정값 | 설명
    ---- | ----
    bridge | 브리지 연결(기본값)을 사용
    none | 네트워크에 연결하지 않는다.
    container:[name &#124; id] | 다른 컨네이너의 네트워크를 사용
    host | 컨테이너가 호스트 OS의 네트워크를 사용
    NETWORK | 사용자 정의 네트워크를 사용
   
* 사용자 정의 네트워크는 docker network create 명령으로 작성
  * 이 네트워크를 작성하려면 Docker 네트워크 드라이버 또는 외부 네트워크 드라이버 플러그인을 사용해야 한다.
  * 똑같은 네트워크에 대해서 여러 컨테이너가 연결 할수 있다.
  * 사용자 정의 네트워크에 연결하면 컨테이너는 컨테이너의 이름이나 IP 주소로 서로 통신을 할 수 있다.
  * 오버레이 네트워크나 커스텀 플러그인을 사용하면 멀티호스트에 대한 연결을 할 수 있다.
  * 컨테이너가 동일한 멀티호스트 네트워크에 연결되어 있으면 이 네트워크를 통해 통신이 가능
  
 * `docker network create -d bridge webap-net`
  * `docker container run --net=webap-net -it centos`
  * 외부 브리지 네트워크 드라이버를 사용하여 'webap-net'이라는 이름의 네트워크를 작성하고, 작성한 네트워크 상에서 컨테이너를 실행함
  

### 자원을 지정하여 컨테이너 생성 및 실행 (docker container run)
 
* `docker container run [자원 옵션] 이미지명:[:태그명] [인수]`

* 주요 옵션
  * 옵션 | 설명
    ---- | ----
    --cpu--shares, -c | CPU의 사용 배분(비율). default:1024
    --memory, -m | 사용할 메모리를 제한하여 실행 (단위는 b, k, m, g 중 하나)
    --volume=[호스트의 디렉토리]:[컨테이너의 디렉토리], -v | 호스트와 컨테이너의 디렉토리를 공유
 
*  컨테이너를 시작할 때 CPU와 메모리의 자원 이용을 제한할 수 있다.

* `docker container run --cpu-shares=512 --memory=lg centos`

* `docker container run -v /Users/asa/webap:/usr/shard/nginx/html nginx`

### 컨테이너를 생성 및 시작하는 환경을 지정 (docker container run)

* `docker container run [환경설정 옵션] 이미지명[:태그명] [인수]`

* 옵션 | 설명
  ---- | ----
  --env=[환경변수], -e | 환경변수를 설정
  --env-file=[파일명] | 환경변수를 파일로부터 설정
  --read-only=[true &#124; false] | 컨테이너의 파일 시스템을 읽기 전용으로 만듦
  --workdir=[path], -w | 컨테이너의 작업 디렉토리를 지정
  --u, --user=[사용자명] | 사용자명 또는 UID를 지정

* ```text
  PS C:\workspace> docker container run -it -e foo=bar centos /bin/bash
  [root@158930b851be /]# set
  ....
  foo=bar
  ```

* ```text
  S C:\workspace> cat env_list
  hoge=fuga
  foo=bar
  S C:\workspace> docker container run -it -env-file=env_list centos /bin/bash
  [root@158930b851be /]# set
  ....
  hoge=fuga
  foo=bar
  ```
  
* ```text
  PS C:\workspace> docker container run -it -w=/tensorflow centos /bin/bash
  [root@3d5daea61de4 tensorflow]# pwd
  /tensorflow
  ```
  
### 가동 컨테이너 목록 표시 (docker container ls)

* `docker container ls [옵션]`
  * docker 상에서 작동하는 컨테이너의 가동 상태 확인
  
* 옵션 | 설명
  ---- | ----
  --all, -a | 실행 중/정지 중인 것도 포함하여 모든 컨테이너를 표시
  --filter, -f | 표시할 컨테이너의 필터링
  --format | 표시 포맷을 지정
  --last, -n | 마지막으로 실행된 n건의 컨테이너만 표시
  --latest, -l | 마지막으로 실행된 컨테이너만 표시
  --no-trunc | 정보를 생략하지 않고 표시
  --quiet, -q | 컨테이너 ID만 표시
  --size, -s | 파일 크기 표시
  
 * docker container ls 명령 실행 결과 항목 의미
  * 항목 | 설명
    ---- | ----
    CONTAINER ID | 컨테이너 ID
    IMAGE | 컨테이너의 바탕이 된 이미지
    COMMAND | 컨테이너 안에서 실행되고 있는 명령
    CREATED | 컨테이너 작성 후 경과 시간
    STATUS | 컨테이너의 상태 (restarting/running/paused/exited)
    PORTS | 할당된 포트 
    NAMES | 컨테이너 이름
    
* `docker container ls -a -f name=test1`

* `docker container ls -a -f exited=0`

* --format 옵션 출력 형식
  * placeholder | 설명
    ---- | ----
    .ID | 컨테이너 ID
    .Image | 이미지 ID
    .Command | 실행 명령
    .CreatedAt | 컨테이너가 작성된 시간
    .RunningFor | 컨테이너의 가동 시간
    .Posts | 공개 포트
    .Status | 컨테이너 상태
    .Size | 컨테이너 디스크 크기
    .Names | 컨테이너명
    .Mounts | 볼륨 마운트
    .Networks |  네트워크명
  
* `docker container ls -a --format "{{.Names}}: {{.Status}}`

* `docker container ls -a --format "table {{.Names}}\t{{.Status}}\t{{.Mounts}}"`


### 컨테이너 가동 확인 (docker container stats)

* `docker container stats [컨테이너 식별자]`

* ```text
  PS C:\workspace> docker stats webserver
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.348MiB / 1.934GiB   0.12%               758B / 0B           7.59MB / 4.1kB      2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.344MiB / 1.934GiB   0.12%               758B / 0B           7.59MB / 4.1kB      2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.344MiB / 1.934GiB   0.12%               758B / 0B           7.59MB / 4.1kB      2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.344MiB / 1.934GiB   0.12%               758B / 0B           7.59MB / 4.1kB      2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.344MiB / 1.934GiB   0.12%               758B / 0B           7.59MB / 4.1kB      2
  ```

* 항목 | 설명
  ---- | ----
  CONTAINER ID | 컨테이너 식별자
  NAME | 컨테이너명
  CPU % | CPU 사용률
  MEM USAGE/LIMIT | 메모리 사용량/컨테이너에서 사용할 수 있는 메모리 제한
  MEM % | 메모리 사용률
  NET I/O | 네트워크 I/O
  BLOCK I/O | 블록 I/O
  PIDS | PID (Windows 컨테이너 제외)
  
  
### 컨테이너 시작 (docker container start)

* `docker container start [옵션] <컨테이너 식별자> [컨테이너 식별자]`


* 옵션 | 설명
  ---- | ----
  --attach, -a | 표준 출력, 표준 오류 출력을 연다.
  --interactive | 컨테이너의 표준 입력을 연다.
  

### 컨테이너 정지 (docker container stop)

* `docker container stop [옵션] <컨테이너 식별자> [컨테이너 식별자]`

* 옵션 | 설명
  ---- | ----
  --time, -t | 컨테이너의 정지 시간을 지정(기본값은 10초)
  
* `docker container stop -t 2 303c9ae42e66
  

### 컨테이너 재시작 (docker container restart)

* `docker container restart [옵션] <컨테이너 식별자> [컨테이너 식별자]`

* 옵션 | 설명
  ---- | ----
  --time, -t | 컨테이너의 재시작 시간을 지정(기본값은 10초)

* --restart : 컨테이너 자동 재실행 하고 싶을 경우 run 명령에 해당 옵션을 사용


### 컨테이너 삭제 (docker container rm)

* `docker container rm [옵션] <컨테이너 식별자> [컨테이너 식별자]`

* 옵션 | 설명
  ---- | ----
  --force, -f | 실행 중인 컨테이너를 강제로 삭제
  --volumes, -v | 활당한 볼륨을 삭제
  

* *docker container prune* : 정지중인 모든 컨테이너 삭제
 

### 컨테이너 중단/재개 (docker container pause/docker container unpause)

* 컨테이너 일시 중단
  * `docker container pause <컨테이너 식별자>`

* 컨테이너 재개
  * `docker container unpause <컨테이너 식별자>`

 


  
  