---
layout: post
title:        도커 스웜(Docker Swarm)
author:       서영원
published : true
comments: true
categories: 도커
---


도커 스웜은 도커 엔진 자체에 내장되어 있습니다. 다음과 같은 명령어를 사용하면 inactive로 되어있는것을 확인 할 수 있습니다.

```
docker info | grep swarm
```

### 도커 스웜 모드 구조
스웜모드는 **매니저 노드**와 **워커 노드**로 구성됩니다. 

* 매니저 노드 : 워커노드를 관리하는 노드
* 워커 노드 : 컨테이너가 생성되고 관리되는 노드

매니저 노드에도 컨테이너가 생성되고 관리되지만 구분해서 사용하는 것이 권장됩니다.


![](/assets/post/docker/swarm-diagram.png)

### 도커 스웜 모드 클러스터 구축

도커 스웜 모드 클러스터를 구축에 대한 실습을하기 위해서 3대의 머신이 필요합니다. 버추얼박스의 가상머신이나 AWS의 EC2, GCP의 Compute Engine을 사용하면되는데 저는 GCP의 Compute Engine에서 ubuntu 인스턴스 3개를 생성하고 사용하겠습니다.


생성한 인스턴스의 정보는 다음과 같습니다.
* 매니저노드
  * instance-1 10.128.0.2
* 워커노드
  * instance-2 10.128.0.3
  * instance-3 10.128.0.4


#### 매니저 노드 생성 및 워커노드 연결
매니저 노드로 사용할 인스턴스에서 swarm init 명령어를 사용해 스웜 클러스터를 시작합니다.

```
docker swarm init --advertise-addr 10.128.0.2
```

위 명령어의 결과로 다음과 같이 명령어(매니저에 접속하기 위한 토큰)가 반환됩니다. 아래의 결과를 복사해 워커노드에서 실행합니다.

```
docker swarm join --token SWMTK... 10.128.0.2:2377
```

다음과 같은 명령어로 매니저노드의 토큰을 확인하거나 새로운토큰발급을 할 수 있습니다.

```
docker swarm join-token manager
docker swarm join-token --rotate manager
```


여기서는 실습환경으로 매니저노드를 한대만 사용하지만 실제운영환경에서는 여러개의 매니저노드를 사용하는것을 권장합니다. 매니저노드의 장애나 부하를 분산하기위함입니다. 그러나 매니저노드 개수가 많아진다고 해서 스웜 클러스터의 성능이 좋아지는것은 아닙니다.

매니저는 홀수로 구성하는 것이 바람직합니다. 절반 이상의 매니저 노드에 장애가 발생하면 클러스터 운영이 중단되는데 홀수로 구성시 짝수일때 보다 매니저 노드 장애를 더 허용하기 때문이고, 일관성을 유지할 수 있기 때문입니다.

클러스터를 구축하면 다음의 명령어로 클러스터내 노드들을 확인할 수 있습니다. 별표(*)가 붙어있는 노드가 매니저 노드입니다.

```
docker node ls
```

다음은 노드를 삭제하는 명령어 입니다. 이는 두단계로 진행됩니다. 
1. 삭제할 워커노드에서 명령어를 실행합니다.
```
docker swarm leave
```

2. 매니저 노드에서 삭제한 워커노드를 삭제합니다.
```
docker node rm instance-2
```

매니저 노드 삭제 명령어는 --force 옵션을 지정합니다. 삭제할 매니저 노드에서 실행합니다.

```
docker swarm leave --force
```

다음은 워커노드를 매니저 노드로 변경하는 명령어 입니다. 
```
docker node promote instance-2
```

다음은 매니저 노드를 워커노드로 변경합니다.
```
docker node demote instance-2
```

### 스웜 모드 서비스

스웜 모드에서 제어하는 단위는 컨테이너가 아닌 **서비스(Service)**입니다. 서비스는 **같은 이미지**에서 생성된 컨테이너의 집합이며 서비스내에는 1개 이상의 컨테이너가 존재할 수 있으며, 컨테이너들을 태스크(Task)라고 합니다.

스웜 스케줄러는 서비스의 정의에 따라 컨테이너를 할당할 적합한 노드를 선정하고 해당 노드에 컨테이너를 분산해서 할당합니다. 이때 함께 생성된 **레플리카(replica)**라고 하며 서비스에 설정된 레플리카의 수만큼의 컨테이너가 클러스터내에 존재해야 합니다. 스웜 매니저는 노드에 장애가 발생해도 다른 노드를 사용해 지정된 레플리카수만큼의 컨테이너를 유지합니다.

![](/assets/post/docker/docker-swarm-replica.png)


#### 서비스 생성
서비스제어에 대한 명령어는 모두 매니저 노드에서 실행합니다.
```
docker service create --name myweb --replicas 2 -p 80:80 nginx
```

다음은 구동중인 서비스 목록을 확인하는 명령어 입니다.
```
docker service ls
```

다음은 서비스의 자세한 정보 확인하는 명령어 입니다. 서비스내 컨테이너 목록, 상태, 할당 노드등을 확인할 수 있습니다.
```
docker service ps myweb
```

다음은 서비스를 삭제하는 명령어 입니다. 당연히 내부컨테이너들을 함께 삭제합니다. 이때 컨테이너 가동유무는 관계없이 무조건 삭제합니다.
```
docker service rm myweb
```

생성된 서비스의 어느 노드를 접근해도, 서비스가 생성되지 않은 노드를 사용해서도 위에서 생성한 nginx에 접근이 가능합니다. 스웜 모드는 라운드 로빈으로 서비스 내 접근할 컨테이너를 결정, 트래픽이나 리소스사용에 따른 로드 밸런싱을 적용해야할 상황에서는 적합하지 않습니다. 실제 로드벨런싱은 노드별 가능합니다.

다음은 서비스내의 nginx컨테이너를 4개로 유지하도록 변경하는 명령어 입니다.
```
docker service scale myweb=4
```

```
docker service ps myweb
```



#### 서비스 롤링 업데이트
스웜 모드는 서비스내의 상태를 일괄 업데이트하는 롤링 업데이트를 지원합니다. 만약 이미지를 특정 버전으로 변경하고 싶다면 다음의 명령어를 사용합니다.
```
docker service update --image nginx:1.11 myweb
```

서비스 생성시 롤링 업데이트 주기, 업데이트를 동시에 진행할 컨테이너 개수, 업데이트 실패시 동작등을 설정할 수 있습니다. 다음은 각 컨테이너 레플리카를 10초단위로 업데이트하며 컨테이너 2개씩 업데이트하는 서비스를 생성하는 명령어 입니다.
```
docker service create --replicas 4 --name myweb --update-delay 10s --update-parallelism 2 nginx
```

다음은 롤링 업데이트 설정을 확인하는 명령어입니다.
```
docker service inspect --pretty myweb
docker inspect --type service myweb
```

## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}
* https://docs.docker.com/engine/install/ubuntu/

