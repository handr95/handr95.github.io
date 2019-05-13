---
title: "docker 기본 명령어"
date: 2019-05-13
categories:
  - docker
toc: true
---

`http://www.yes24.com/Product/Goods/64320759?Acode=101 참고`

### docker 이미지 조작

* docker hub (https://hub.docker.com)
  * 소스코드 관리 툴과 연계하여 코드를 빌드하는 기능이나 
  * 실행 가능한 애플리케이션의 이미지를 관리하는 기능을 갖춘 Docker 공식 레포지토리 서비스
  * 물리 서버, 가상 머신, 클라우드든 Docker 이미지를 배포 가능

* **이미지명 [:태그명]** : docker 이미지 지정
  * ex) debian:7 은 Debian의 버전 7을 베이스 이미지를 갖고있는 Docker 이미지를 의미
  * 태그명에 latest를 지정할 경우 레포지토리에 공개되어 있는 최신판 이미지라는 것을 의미한다.
  * latest 태그로 지정한 이미지는 릴리스마다 달라지므로 제품 환경에서 이용할 때는 버전을 명시적으로 지정하여 사용하는것이 좋다.
   
* **docker image pull [옵셥] 이미지명[:태그명]** : 이미지 다운로드
  * `docker image pull centos:7` : 
  * `docker image pull -a centos` : 태그명을 생략하면 latest(최신판)을 다운로드한다.
  * -a : 모든 태그를 다 다운로드한다. 태그명 지정X
  * `docker image pull gcr.io.tensorflow/tensorflow` : 이미지명에 url을 지정할 수도 있다.
 
* **docker image ls [옵션] [레포지토리명]**
  * 옵셩 | 설명
    ---- | ----
    -all, -a | 모든 이미지를 표시
    --digests | 다이제스트를 표시할지 말지
    --no-trunc | 결과를 모두 표시
    --quiet, -q | Docker 이미지 ID만 표시
  * ```text
      PS C:\workspace> docker images
      REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
      nginx                      latest              53f3fd8007f7        2 days ago          109MB
  ```
  * docker 이미지를 작성하면 고유한 이미지 ID(IMAGEID)가 부여됨 (랜덤한 문자열)
  * 또한 docker 레지스트리에 업로드한 이미지는 이미지를 고유하게 식별하기 위한 다이제스트가 부여됩니다.
  * 항목|설명
    ---- |----
    REPOSITORY|이미지 이름
    TAG | 이미지 태그명
    IMAGE ID | 이미지 ID
    CREATED | 작성일
    SIZE | 이미지 크기 
 
 * Docker Content Trust(DCT) 기능을 사용하면 Docker 이미지의 정당성을 확인할 수 있다.
  * https://docs.docker.com/engine/security/trust/content_trust/#image-tags-and-content-trust
  
 * **docker image inspect** : 이미지 상세 정보 확인
  * 확인할수 있는 주요 정보로는 이미지ID, 작성일, Docker 버전, CPU 아키텍쳐 등이 있다.
  * Id 값을 확인하고 싶을 경우 `docker image inspect --format="{{ .Id}}" centos:7`
 
* **docker image tag** : 이미지 태그 설정
  * 이미지에 표식에는 식별하기 쉬운 버전명을 붙이는 것이 일반적이다.
  * Docker Hub에 작성한 이미지를 등록하려면 `<Docker Hub 사용자명>/이미지명:[태그명]` 과 같은 규칙으로 작성해야한다.
  * ```text
  PS C:\workspace> docker images
  REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
  nginx                      latest              53f3fd8007f7        5 days ago          109MB
  PS C:\workspace> docker image tag nginx handr95/webserver:1.0
  PS C:\workspace> docker images
  REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
  handr95/webserver          1.0                 53f3fd8007f7        5 days ago          109MB
  nginx                      latest              53f3fd8007f7        5 days ago          109MB
  ```
  * 이미지 아이디가 동일 -> 이미지에 별명을 붙이는 것이지 이미지 자체를 복사하거나 이름을 바꾼 것이 아니다.
  
* **docker search** : 이미지 검색
  * `docker search [옵션] <검색 키워드>`
  * 옵션 | 설명
    ---- | ----
    -no-trunc | 결과를 모두 표시
    --limit | n건의 검색 결과를 표시
    --filter=starts=n | 즐겨찾기의 수(n이상)를 지정
  * Docker Hub에 공개되어있는 검색어에 관한 Docker 이미지를 검색함
  * ```text
    PS C:\Users\darom> docker search nginx
    NAME                                        DESCRIPTION                                      STARS       OFFICIAL       AUTOMATED
    nginx                                       Official build of Nginx.                         11386       [OK]
    jwilder/nginx-proxy                         Automated Nginx reverse proxy for docker con…   1600                       [OK]
    richarvey/nginx-php-fpm                     Container running Nginx + PHP-FPM capable of…   713                        [OK]
    jrcs/letsencrypt-nginx-proxy-companion      LetsEncrypt container to use with nginx as p…   509                        [OK]
    .....
    ```
  * 항목 | 설명
    ---- | ----
    NAME | 이미지 이름
    DESCRIPTION | 이미지 설명
    STARS | 즐겨찾기 수
    OFFICIAL | 공식 이미지 여부
    AUTOMATED | Dockerfile 바탕으로 자동 생성된 이미지 여부
   
* **docker image rm** : 이미지 삭제
  * `docker image rm [옵션] 이미지명 [이미지명]`
  * 옵션 | 설명
    ---- | ----
    --force, -f | 이미지를 강제로 삭제
    --no-prune | 중간 이미지를 삭제하지 않음
  * 이미지명은 [REPOSITORY] 또는 [IMAGE ID]를 지정함  
  * 이미지 ID는 고유하므로 처름 3자리 수만 입력해도 됨
  * 여러개의 이미지를 삭제 하고 싶을 경우 이미지명을 스페이스로 구분
  * `docker image prune [옵션]` : 사용하지 않는 이미지 삭제
  * 욥션 | 설명
    --all, -a | 사용하지 않은 이미지를 모두 삭제
    --force. -f | 이미지를 강제로 삭제
  * 사용하지 않는 도커 이미지는 디스크 용량을 차지하기 때문에 정기적으로 삭제해주는 것이 좋다.

* **docker loing** : Docker Hub에 로그인
* `docker login [옵션] [서버]`
* 옵션 | 설명
  ---- | ----
  --password, -p | 비밀번호
  --username, -u | 사용자명
  
* **docker image push** : 이미지 업로드
  * `docker image push 이미지명[:태그명]` 
  * ```text
    PS C:\Users\darom> docker image push handr95/webserver:1.0
    The push refers to repository [docker.io/handr95/webserver]
    332fa54c5886: Mounted from library/nginx
    6ba094226eea: Mounted from library/nginx
    6270adb5794c: Mounted from library/nginx
    1.0: digest: sha256:e770165fef9e36b990882a4083d8ccf5e29e469a8609bb6b2e3b47d9510e2c8d size: 948
    ```

* **docker logout** : Docker Hub에 로그아웃
  * `docker logout [서버명]`
   
  

    
  
  
 
    