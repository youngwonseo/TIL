---
layout: post
title:        도커 볼륨
author:       서영원
published : true
comments: true
categories: docker
tags: [도커, 도커 볼륨, docker, docker volume]
---

도커 운영환경에서 컨테이너 내부에 데이터가 저장될 경우, 예로 들면 MySql 이미지를 컨테이로 운영하면서 데이터가 저장될 경우 컨테이너를 삭제하면 MySql에 저장된 데이터도 함께 삭제됩니다. 데이터가 삭제되는것을 방지하고 컨테이너 내부의 데이터에 대한 영속성(persistence)를 제공하기 위해 도커엔진은 도커 볼륨 기능을 제공합니다.


도커 볼륨은 3가지로 제공됩니다.
* 호스트 볼륨
* 볼륨 컨테이너
* 도커 볼륨

<br/>

### 호스트 볼륨
호스트 볼륨은 컨테이너 내부의 디렉토리와 호스트 머신의 디렉토리와 공유하는 방법입니다. -v옵션을 사용하며 \[호스트의 공유 디렉터리\]:\[컨테이너의 공유 디렉터리\]로 옵션값을 지정합니다. 경로가 없을경우 경로를 생성합니다.

```
docker run -v /Users/youngwon/mysql:/val/lib/mysql mysql:5.7
```
위 경로에서 /Users/youngwon/mysql은 제 맥북의 경로이고 /val/lib/mysql은 mysql이미지의 컨테이너 내부에서 mysql의 데이터가 저장되는 디렉터리입니다. 즉 사용하는 어플리케이션의 이미지에 따라 컨테이너 공유 디렉터리가 결정됩니다. MongoDB의 경우 다음과 같은 경로를 볼륨으로 설정합니다.
```
docker run -v /Users/youngwon/mongodb:/data/db mongo
```
![](/assets/post/docker/docker-volume-01.png)

컨테이너가 삭제되더라도 호스트에 생성된 /Users/youngwon/mysql은 삭제되지 않기 때문에 같은 볼륨옵션을 지정해서 컨테이너를 생성하면 mysql의 데이터가 그대로 존재하는것을 확인할 수 있습니다. 이는 볼륨 지정 시 컨테이너 내부 데이터가 호스트에 복사되었기 때문입니다.

여러경로의 디렉토리를 공유하고 싶은경우 여러개의 볼륨옵션을 지정할 수 있습니다.

<br/>

### 볼륨 컨테이너
볼륨을 사용한는 두 번째 방법은 볼륨을 사용하는 컨테이너와 볼륨을 공유하는 것입니다. --volumes-from옵션을 사용해서 볼륨을 사용하는 다른 컨테이너를 지정하는 것입니다. 

```
docker run --name volume_mysql -d -v /Users/youngwon/mysql:/val/lib/mysql mysql:5.7
docker run --volumes-from volume_mysql mysql:5.7
```
첫 번째 명렁어는 호스트와 볼륨을 공유하는 컨테이터를 생성하는 명령어이고 두 번째 명령어는 앞에서 생성한 컨테이너와 볼륨을 공유하는 컨테이너를 생성하는 명령어입니다.

![](/assets/post/docker/docker-volume-02.png)

volume_mysql과 같이 볼륨을 담당하고 다른 역할을 하지 않는 컨테이너를 볼륨 컨테이너라고 합니다(예에서는 mysql이미지를 사용하기 때문에 볼륨역할만 한다고는 할 수 없습니다).

<br/>

### 도커 볼륨
도커 볼륨은 docker volume create 명령어로 볼륨을 사용하는 방법입니다. 
```
docker volume create --name myvolume
docker volume ls
```
볼륨을 생성할 때 플러그인 드라이버를 설정해 여러종류의 스토리지 백엔드를 사용할 수 있지만 기본은 local입니다. local은 로컬 호스트에 저장되며 도커엔진에 의해 생성되고 삭제됩니다. 다음은 위에서 생성한 도커 볼륨을 사용하는 컨테이너입니다.
```
docker run -v myvolume:/root/
```

여기서 생성한 볼륨은 디텍터리 하나에 상응하는 단위로서 도커 엔진에서 관리합니다. docker inspect 명령어를 사용하면 볼륨이 실제 어디에 저장되는지 알 수 있습니다.
```
docker inspect myvolume
```

다음은 컨테이너가 어떤 볼륨을 사용하는지 확인하는 명령어 입니다.
```
docker container inspect volume_mysql
```
컨테이너를 삭제한다고 해서 볼륨이 삭제되지는 않습니다. 모든 볼륨을 삭제하고 싶을경우 다음 명령어를 사용합니다.
```
docker volume prune
```
<br/>

## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}


