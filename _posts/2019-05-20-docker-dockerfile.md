---
title: "dockerfile을 사용한 코드에 의한 서버 구축"
date: 2019-05-13
categories:
  - docker
toc: true
---

`http://www.yes24.com/Product/Goods/64320759?Acode=101 참고`


## dockerfile을 사용한 구성 관리

### dockerfile이란


* docker 상에서 작동시킬 컨테이너의 구성 정보
  * 베이스가 될 docker 이미지
  * docker 컨테이너 안에서 수행한 조작(명령)
  * 환경변수 등의 설정
  * docker 컨테이너 안에서 작동시켜둘 데본 실행

* docker build : dockerfile에 기술된 구성 정보를 바탕으로 docker 이미지를 작성함



### dockerfile의 기본 구문

* dockerfile은 텍스트 형식의 파일
  * 확장자는 필요 없다.
  * dockerfile 이외의 파일명으로도 작동하지만, 이미지를 빌드할때 파일명을 명시해야함

* dockerfile 기본 서식
  * `명령 인수`

* 명령 | 설명
  ---- | ----
  FROM | 베이스 이미지 지정
  RUN | 명령 실행
  CMD | 컨테이너 실행 명령
  LABEL | 라벨 설정
  EXPOSE | 포트 익스포트
  ENV | 환경변수
  ADD | 파일/디렉토리 추가
  COPY | 파일 복사
  ENTRYPOINT | 컨테이너 실행 명령
  VOLUME | 볼륨 마운트
  USER | 사용자 지정
  WORKDIR | 작업 디렉토리
  ARG | Dockerfile 안의 변수
  ONBUILD | 빌드 완료 후 실행되는 명령
  STOPSIGNAL | 시스템 콜 시그널 설정
  HEALTHCHECK | 컨테이너의 헬스 체크
  SHELL | 기본 쉘 설정

* Dockerfile의 주석 서식
  * ```text
    # 이것은 주석입니다.
    명령 인수 # 이것도 주석입니다.
    ```

### Dockerfile 작성

* FROM 명령 : Docker 컨테이너를 어떤 Docker 이미지로부터 생성할지에 대한 정보 기술
  * ```text
    FROM [이미지명]
    FROM [이미지명]:[태그명]
    FROM [이미지명]@[다이제스트]
    ```
  * 태그명 생략하면 베이스 이미지의 최신 버전이 적용됨

* CentOS를 베이스 이미지로 한 Dockerfile
  * ```text
    # 베이스 이미지 설정
    FROM centos:centos7
    ```

* 고유한 이미지를 사용하려면 다이제스트를 이용
  * 다이제스트 : Docker Hub에 업로드하면 자동으로 부여되는 식별자를 말함

* 다이제스트를 지정한 Dockerfile
  * ```text
    FROM tensorflow/tensorflow@sha256:273cd3c514fab7....
    ```


## Dockerfile의 빌드와 이미지 레이어

### Dockerfile로부터 Docker 이미지 만들기

* docker build 명령의 서식
  * Dockerfile로부터 이미지를 생성
  * `docker build -t [생성할 이미지명]:[태그명] [Dockerfile의 위치]`

* Dockerfile 작성
  * ```text
    FROM centos:centos7
    ```
  * ```text
    PS C:\workspace\git_blog> docker build -t sampler:1.0 ..\git_blog\
    Sending build context to Docker daemon  445.4kB
    Step 1/1 : FROM centos:centos7
    centos7: Pulling from library/centos
    8ba884070f61: Pull complete
    Digest: sha256:b5e66c4651870a1ad435cd75922fe2cb943c9e973a9673822d1414824a1d0475
    Status: Downloaded newer image for centos:centos7
     ---> 9f38484d220f
    Successfully built 9f38484d220f
    Successfully tagged sampler:1.0
    SECURITY WARNING: You are building a Docker image from Windows against a non-Windows Docker host.
    All files and directories added to build context will have '-rwxr-xr-x' permissions.
    It is recommended to double check and reset permissions for sensitive files and directories.
    PS C:\workspace\git_blog> docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    sampler             1.0                 9f38484d220f        2 months ago        202MB
    ```

* -f : 파일명 지정
  * `docker buildi -t sample -f Dockerfile.base .`
  * . : 커맨드 디렉토리


* - : 표준 입력에서의 빌드
  * `docker build - < Dockerfile`
  * 빌드에 필요한 파일 포함 시킬 수 없다. ex) ADD 명령으로 이미지 않에 파일 추가 못함


* docker는 이미지를 빌드할 때 자동으로 중간 이미지를 생성한다.
  * 다른 이미지를 빌드할 때 중간 이미지를 내부적으로 재이용함으로써 빌드를 고속으로 수행
  * 이미지를 재이용하고 있을 경우, 'Using cache'라고 표시된다.
  * 캐시를 이용하고 싶지 않은 경우 docker build 명령에서 --no-cache 옵션을 사용한다.


### Docker 이미지 레이어 구조

* dockerfile
  * ```text
    # STEP:1 Ubuntu (베이스 이미지)
    FROM ubuntu:latest

    # STEP:2 Nginx 설치
    RUN apt-get update && apt-get install -y -q nginx

    # STEP:3 파일 복사
    COPY index.html /usr/share/nginx/html/

    #STEP:4 Nginx 시작
    CMD ["nginx", "-g", "daemon off;"]
    ```



## 멀티 스테이지 빌드를 사용한 애플리케이션 개발


### Dockerfile 만들기

* Dockerfile
  * ```text
    # 1. Build Image
    FROM golang:1.8.4-jessie AS builder

    #Install dependencies
    WORKDIR /go/src/github.com/asashiho/greet
    RUN go get -d -v github.com/urfave/dli

    # Build modules
    COPY main.go .
    RUN GOOS=linux go build -a -o greet .

    # -------------------------------
    # 2. Production Image
    FROM busybox
    WOEKDIR /opt/greet/bin

    # Deploy modules
    COPY --from=builder /go/src/github.com/asashiho/greet/ .
    ENTRYPOINT ["./greet"]
    ```

* Dockerfile 구분
  * 개발 환경용 Docker 이미지
    * 개발용 언어 Go를 베이스 이미지로 작성
    * 개발에 필요한 버전을 설치하여 로컬 환경에 있는 소스코드를 컨테이너 안으로 복사
    * 이 소스 코드를 go build 명령으로 빌드하여 'greet'이라는 이름의 실행 가능 바이너리 파일을 작성
  * 제품 환경용 Docker 이미지
    * 'busybox'라는 이름의 실행 가능 바이너리 파일을 제품 환경용 Docker 이미지로 복사
    * --from 옵션을 이용하여 'builder'라는 이름의 이미지로부터 복사를 한다는 것을 선언함
    * 마지막으로 복사한 실행 가능 바이너리 파일을 실행하는 명령을 적습니다.

