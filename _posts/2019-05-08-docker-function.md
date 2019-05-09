---
title: "docker의 기능"
date: 2019-05-08 
categories:
  - docker
toc: true
---

`http://www.yes24.com/Product/Goods/64320759?Acode=101 참고`

### docker 컴포넌트


* docker engine : 도커의 핵심 기능
	* docker 이미지를 생성하고 컨테이너를 기동시키기 위한 docker 의 핵심 기능.
	* docker 명령의 실행이나 dockerfile에 의한 이미지도 생성합니다.

* docker registry : 이미지 공개 및 공유
	* 컨테이너의 바탕이 되는 docker 이미지를 공개 및 공유하기 위한 레지스트리 기능
	* docker의 공식 레지스트리 서비스인 docker hub도 이 docker registry를 사용하고 있음.

* docker componse : 컨테이너 일원 관리
	* 여러 개의 컨테이너 구성 정보를 코드로 정의하고, 명령을 실행함으로써 애플리케이션의 실행 환경을 구성하는 컨테이너들을 일원 관리하기 위찬 툴

* docker machine : docker 실행 환경 구축
	* 로컬 호스트용인 VirtualBox를 비롯하여 Amazon Web Services EC2나 Microsoft Azure와 같은 클라우드 환경에 Docker의 실행 환견을 명령으로 자동 생성하기 위한 툴

* doekcr swarm : 클러스터 관리
	* docker swarm은 여러 docker 호스트를 클러스터화하기 위한 툴입니다.
	* docker swarm에서는 클러스터를 관리하거나 API를 제공하는 역할은 Manager가, Docker 컨테이너를 실행하는 역할은 Node가 담당합니다.
	* 또한 오픈소스인 Kubernates도 이용할 수 있습니다.


### docker 작동 구조

* namespace : 컨테이너를 구획화하는 장치
	* docker는 컨테이너라는 독립된 환경을 만들고, 그 컨테이너를 구획화하여 애플리케이션의 실행 환경을 만든다.
	* 이 컨테이너를 구획하는 기술은 Linux 커널의 namespace라는 기능을 사용하고 있다.
	* namespace는 한 덩어리의 데이터에 이름을 붙여 분할함으로써 충돌 가능성을 출이고, 쉽게 참조할 수 있게 하는 개념입니다.
	* 그래서 이름공간이 다르면 동일한 이름이라고 다른 실체로 처리됩니다.

* pid namespace
	* pid란 linux에서 각 프로세스에 할당된 고유한 ID를 말합니다.
	* pid namespace는 pid와 프로세스를 격리시킵니다.
	* namespace가 다른 프로세스끼리는 서로 액세스 할 수 없습니다.

* network namespace
	* network namespace는 네트워크 디바이스, IP 주소, 포트 번호, 라우팅 테이블, 필터링 테이블 등과 같은 네트워크 리소스를 격리된 namespace마다 독립적으로 가질 수 있습니다.
	* 이 기능을 사용하면 호스트 OS 상에서 사용 중인 포트가 있더라고 컨테이너 안에서 동일한 번호의 포트를 사용할 수 있습니다.

* uid namespace
	* uid namespace는 uid(사용자 id), gid(그룹 id)를 namespace별로 독립적으로 가질 수 있습니다.
	* namespace 안과 호스트 os상의 uid/gid가 서로 연결되어 이름공간 안과 밖에서 서로 다른 uid/gid를 가질 수 있습니다.
	* 예를 들어 namespace 안에서는 uid/gid가 0인 root 사용자를, 호스트 os 상에서는 일반 사용자로서 취급할 수 있습니다.
	* 이것은 namespace 안의 관리자 계정은 호스트 os에 대해서는 관리 권한을 일절 갖지 않는다는 것을 의미하므로 보안이 뛰어난 환경으로 격리시킬 수 있습니다.

* mount namespace
	* linux에서 파일 시스템을 사용하기 위해서는 마운트가 필요합니다.
	* 마운트란 컴퓨터에 연결된 기기나 기억장치를 os에 인식시켜 이용 가능한 상태로 만드는 것을 말합니다.
	* mount namespace는 마운트 조작을 하면 namespace 안에 격리된 파일 시스템 트리를 만듭니다.
	* 다른 namespace 기능과 마찬가지로 namespace 안에서 수행한 마운트는 호스트 os나 다른 namespace에서는 액세스할 수 없게 되어 있습니다.

* uts namespace
	* uts namespace는 namespace별로 호스트명이나 도메인명을 독자적으로 가질 수 있습니다.

* ipc namespace
	* ipc namespace는 프로세스 간의 통신(ipc) 오브젝트를 namespace별로 독립적으로 가질 수 있습니다.
	* ipc는 system v 프로세스 간의 통신 오브젝트라고 하는 공유 메모리나 세마포어/메시지 큐를 말합니다.
	* 세마포어란 프로세스가 요구하는 자원 관리에 이용되는 베타제어 장치이며, 메시지 큐란 여러 프로세스 간에서 비동기 통신을 할 때 사용되는 큐잉 장치입니다.
	* docker는 이러한 namespace장치를 사용하여 호스트 상에서 컨테이너를 가상적으로 격리시킵니다.
	* docker를 사용할 때 특별히 의식할 필요는 없지만 docker의 구조를 이해해 두는 것을 중요합니다.


* cgroups : 릴리스 관리 장치
	* docker에서는 물리 머신 상의 자원을 여러 컨테이너가 공유하여 작동합니다.
	* 이때 linux 커널의 기능인 'control groups(cgroups)' 기능을 사용하여 자원의 할당 등을 관리합니다.
	* linux에서는 프로그램을 프로세스로서 실행합니다.
	* 프로세스는 하나 이상의 스레드 모음으로 움직힙니다.
	* cgroups는 프로세스와 스레드를 그룹화하여, 그 그룹 안에 존재하는 프로세스와 스레드에 대한 관리를 수행하기 위한 기능입니다.
	* 예를 들어 호스트 os의 cpu나 메모리와 같은 자원에 대해 그룹별로 제한을 둘 수 있습니다.
	* cgroups로 컨테이너 안의 프로세스에 대해 자원을 제한함으로써 예를 들면 어떤 컨테이너가 호스트 os의 자원을 모두 사용해 버려서 동일한 호스트 os 상에서 가동되는 다른 컨테이너에 영향을 주는 일을 막을 수 있습니다.
	* cgroups는 계층 구조를 사용하여 프로세스를 그룹화하여 관리할 수 있습니다.
	* 예를 들어 사용자 애플레케이션과 서버와 같은 데몬 프로세스를 나눠, 각각의 그룹에 CPU 사용량을 할당할 수 있습니다.
	* cgroups의 부모자식 관계에서는 자식이 부모의 제한을 물려받습니다.
	* 예를 들어 자식이 부모의 제한을 초과하는 설정을 하더라도 부모 cgroups의 제한에 걸립니다.


---


* 네트위크 구성 : 가상 브리지/가상 NIC
	* linux는 docker를 설치하면 서버의 물리 NIC가 docker0이라는 가상 브리지 네트워크로 연결됩니다.
	* 이 docker0은 Docker를 실행시긴 후에 디폴트로 만들어집니다. 
	* docker 컨테이너가 실행되면 컨테이너에 172.17.0.0/16이라는 서브넷 마스크를 가진 프라이빗 IP 주소가 eth0으로 자동으로 할당됩니다.
	* 이 가상 NIC는 OSI 참조 모델의 리이어 2인 가상 네트워크 인터페이스로. 페어인 NIC와 터널링 통신을 합니다.
	* docker 컨테이너와 외부 네트워크가 통신을 할 때는 가상 브리지 docker0과 호스트 os의 물리 NIC에서 패킷을 전송하는 장치가 필요합니다.







