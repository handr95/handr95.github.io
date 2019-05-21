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
    PS C:\workspace> docker build -t sample:1.0 /home/docker/sample
    ```



