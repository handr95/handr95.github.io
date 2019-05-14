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


  
  