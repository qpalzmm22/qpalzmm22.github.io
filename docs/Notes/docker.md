# 제대로 배우는 도커를 읽고

## Container란

>> Containers are a lightweight and portable store for an application and its 
dependencies

- 컨테이너의 특성
  - 호스트 OS자원을 사용하여 자원을 좀 더 효율적으로 사용 가능하다.
  - 운영환경의 변경을 쉽게 할 수 있다 (portability 제공)
  - 가볍다. (1번의 이유로 많은 수의 container들을 사용 가능)
  - 최종 사용자와 개발자가 클라우드에도 배포할 수 있다. 2번과 비슷하게 환경까지 다 포함시켜서 보낼 수 있다는 뜻 (?)

외부 환경을 완벽하게 Emulate 하려는 VM과 달리 특히 container는 `이식성`과 `독립성`에 목적을 둔다.

Container를 설명하는데 이 그림이 빠질 수 없다.
![VM](https://www.docker.com/sites/default/files/d8/2018-11/docker-containerized-appliction-blue-border_2.png)
![Container](https://www.docker.com/sites/default/files/d8/2018-11/container-vm-whatcontainer_2.png)

- VM는 syscall들을 interpreting 하고 OS와 hardware에 대한 접근을 제어한다. 또한 각각의 OS 전체와 library를 가지고 있어야 한다. 하지만 container는 항상 호스트와 같은 커널에서 실행된다(컨테이너 내부의 process가 호스트의 프로세스들과 동일하다).

## Container의 역사
- UNIX 에서는 chroot 명령어를 통해 독립도니 파일 시스템을 제공하였다. FreeBSD에서 jail 유틸리티를 제공하였고 2001년에 Solaris에서 자사에서만 사용 가능한 컨테이너화 기술을 개발하였다. Parrallels Inc. 에서는 Virtuozzo 컨테이너 기술을 발표하였고 이후에 2005년에 OpenVZ라는 이름으로 오픈소스화 하였다. 이후에 구글이 Linux Kernel을 위한 CGroups의 개발을 시작함과 동시에 구글의 인프라를 컨테이너로 옮기는 작업을 시작하였다. 이후에 LXC(Linux Container) 프로젝트는 2008년에 완벽한 컨테이너화 솔루션을 제공하였고 2013년에 Docker가 Contianerizing의 마지막 조각을 맞추면서 주류기술로 떠올랐다.

## Container 이름의 유래
- 선착장에서 선적할때 물건의 규격이 달라 선적하기 어려워서 만들어 진것이 컨테이너. 예를 들어 이사를 하기 위해 우리는 짐을 어디다가 짐을 넣는가? 바로 박스, 즉 컨테이너에 넣는다. 그러는 이유는 다양한 크기와 특성을 가진 물품들을 추상화 할 수 있기 때문인데 이로 인해 배송에 overhead를 줄일 수가 있다. 이와 같은 특성을 IT로 옮긴 기술이 docker이다.    

