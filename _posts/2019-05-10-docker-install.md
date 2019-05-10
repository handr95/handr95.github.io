---
title: "docker 설치와 튜토리얼"
date: 2019-05-10
categories:
  - docker
toc: true
---

`http://www.yes24.com/Product/Goods/64320759?Acode=101 참고`

### docker의 클라이언트 툴

docker는 Linux 커널 기능을 사용하기 때문에 보통은 Linux 배포판 상에서 작동한다.
하지만 개발 환경에서 이용하기 위한 클라이언트 PC용 툴을 제공한다.

* docker for windows
  * window 10 이후 부터 사용가능.
  * microsoft가 제공하는 하이퍼바이저인 x64용 가상화 시스템인 'Hyper-V'를 사용
  * OS의 설정에서 Hyper-V를 유효화하면 Oracle VirtualBox 등과 같은 다른 가상화 툴은 사용할 수 없다.
  * 그외의 작동 안되는 머신들은 Oracle이 제공하는 VirtualBox를 사용한 'Docker Toolbox'도 제공한다.
  * Docker for Windows 18.02.0-ce Edge 이후 버전에는 Docker 컨테이너의 오케스트레이션 툴인 'Kubernetes'가 포함되어있다.
  

### hello world

* **docker container run** : docker 컨테이너를 작성 및 실행
  * `docker container run <Docker 이미지명> <실행할 명령>*` 
  * Docker 이미지 : 바탕이 되는 docker 이미지
  * 실행할 명령 : 컨테이너 안에서 실행할 명령
  * ex) docker container run ubuntu:latest /bin/echo 'Hello world'
    * 우분투 최신버전(latest)의 이미지 바탕으로 docker 컨테이너 작성 및 실행한 수 작성한 컨테이너 안에서 'Hello world' 표시
  * docker 이미지가 로컬 환경에 있는지 확인 후 없다면, Docker 레파지토리에서 docker 이미지를 다운로드한다.
  
* **docker version** : 도커 버전 확인
  * Docker 버전, Go 언어 버전, od, 아키텍처 확인 가능
  * Docker는 클라이언트/서버 아키텍처를 채택하고 있어서 Docker 클라이언트 Docker 서버가 Remote API를 경유하여 연경되어 있다.
  * 따라서 Docker 명령은 서버로 보내져 처리된다.
  ```text
    PS C:\workspace> docker version
    Client: Docker Engine - Community
     Version:           18.09.2
     API version:       1.39
     Go version:        go1.10.8
     Git commit:        6247962
     Built:             Sun Feb 10 04:12:31 2019
     OS/Arch:           windows/amd64
     Experimental:      false
    
    Server: Docker Engine - Community
     Engine:
      Version:          18.09.2
      API version:      1.39 (minimum version 1.12)
      Go version:       go1.10.6
      Git commit:       6247962
      Built:            Sun Feb 10 04:13:06 2019
      OS/Arch:          linux/amd64
      Experimental:     false
   ```

* **docker system info** : Docker 실행 환경 확인
  * 컨테이너 수, docker 버전, 스토리지 드라이버, OS 종류, 아키텍처 등등..
  ```text
  PS C:\workspace> docker system info
  Containers: 1
   Running: 1
   Paused: 0
   Stopped: 0
  Images: 42
  Server Version: 18.09.2
  Storage Driver: overlay2
   Backing Filesystem: extfs
   Supports d_type: true
   Native Overlay Diff: true
  Logging Driver: json-file
  Cgroup Driver: cgroupfs
  Plugins:
   Volume: local
   Network: bridge host macvlan null overlay
   Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
  Swarm: inactive
  Runtimes: runc
  Default Runtime: runc
  Init Binary: docker-init
  containerd version: 9754871865f7fe2f4e74d43e2fc7ccd237edcbce
  runc version: 09c8266bf2fcf9519a651b04ae54c967b9ab86ec
  init version: fec3683
  Security Options:
   seccomp
    Profile: default
  Kernel Version: 4.9.125-linuxkit
  Operating System: Docker for Windows
  OSType: linux
  Architecture: x86_64
  CPUs: 2
  Total Memory: 1.934GiB
  Name: linuxkit-00155ddb380d
  ID: ULHX:DF3J:L2YL:VQIC:RBAN:6D6S:N6C4:ZQO6:LVRH:4EVU:UFZY:E3VL
  Docker Root Dir: /var/lib/docker
  Debug Mode (client): false
  Debug Mode (server): true
   File Descriptors: 33
   Goroutines: 62
   System Time: 2019-05-10T07:48:40.7732591Z
   EventsListeners: 1
  Registry: https://index.docker.io/v1/
  Labels:
  Experimental: false
  Insecure Registries:
   127.0.0.0/8
  Live Restore Enabled: false
  Product License: Community Engine
  ```
  
* **docker system df** : Docker 디스크 이용 상황
  ```text
  PS C:\workspace> docker system df
  TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
  Images              3                   1                   1.567GB             188.2MB (12%)
  Containers          1                   1                   4.398GB             0B (0%)
  Local Volumes       0                   0                   0B                  0B
  Build Cache         0                   0                   0B                  0B
  ```
  * -v : 상세 내용 확인 

### 웹 서버 작동

* **Nginx** : 대량의 요청을 처리하는 대규모 사이트에서 주로 이용
  * 리버스 프록시나 로드밸런서와 같은 기능도 갖고 있음.
  * https://nginx.org/
  
* docker 이미지 다운
  * docker 컨테이너 시작하기 위해서는 Docker 컨테이너의 바탕이 되는 'docker 이미지'가 필요
  * https://hub.docker.com/_/nginx/
  * docker pull nginx : 도커 이미지 다운로드
  ```text
  PS C:\workspace> docker pull nginx 
  Using default tag: latest
  latest: Pulling from library/nginx
  743f2d6c1f65: Pull complete
  6bfc4ec4420a: Pull complete
  688a776db95f: Pull complete
  Digest: sha256:1d0dfe527f801c596818da756e01fa0e7af4649b15edc3eb245e8da92c8381f8
  Status: Downloaded newer image for nginx:latest
  ```
  * docker images (= docker image ls) : 도커 이미지 확인
  ```text
  PS C:\workspace> docker images
  REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
  nginx                      latest              53f3fd8007f7        2 days ago          109MB
  ```
  
* Nginx 작동
  * docker 이미지 'nginx'를 사용하여 'webserver'라는 이름의 docker 컨테이너를 기동시키는 예
  * 브라우저 http(80 port)에 대한 엑세스 허가 하기 위해 -p 옵션 쿱임
  * -p : 호스트에 연결된 컨테이너의 특정 포트를 외부에 노출. 보통 웹 서버의 포트를 노출할 때 주로 사용. 
  ```text
  PS C:\workspace> docker container run --name webserver -d -p 80:80 nginx
  303c9ae42e66fdc208cf4624ecebfa2a286c4b67aeadfa08ea0f380608d2e7df  
  ```
  * docker ps (= docker container ps) : docker로 기동시킨 nginx 서버의 상태 확인
  ```text
  PS C:\workspace\git_blog\_posts> docker ps
  CONTAINER ID        IMAGE        COMMAND                   CREATED             STATUS              PORTS                     NAMES
  303c9ae42e66        nginx        "nginx -g 'daemon of…"   15 minutes ago      Up 15 minutes       0.0.0.0:80->80/tcp        webserver
  ```
  * 'webserver'라는 이름의 Docker 컨테이너에서 Nginx 서버 프로세스가 시작되어, 컨테이너 80번 포트를 전송하고 있다는 것을 알 수 있음
  * docker container stats 명령으로 확인할 수 있습니다.
  ```text
  PS C:\workspace> docker container stats webserver
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
  303c9ae42e66        webserver           0.00%               2.246MiB / 1.934GiB   0.11%               1.32kB / 0B         3.84MB / 0B         2
  ```

* nginx의 기동 및 정지
  * docker stop <컨테이너 이름> : 서버 프로세스 정지
  * `docker stop webserver`