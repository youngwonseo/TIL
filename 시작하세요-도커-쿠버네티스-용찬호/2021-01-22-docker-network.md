---
layout: post
title:        도커 네트워크
author:       서영원
published : true
comments: true
categories: docker
tags: [도커, 도커 네트워크, docker, docker network]
---

도커내에서 ifconfig를 실행하면 다음과 같이 eth0과 lo네트워크 인터페이를 확인할 수 있습니다.

![](/assets/post/docker/docker-network-01.png)


* 도커는 컨테이너에 내부 IP를 순차적으로 할당
* 이 IP는 컨테이너를 재시작할 때마다 변경될 수 있음
* 이 IP는 도커가 설치된 호스트, 즉 내부망에서만 사용가능한 IP
* 호스트는 도커 컨테이너에 외부 네트워크를 제공하기 위해 veth(virtual eth)로 시작하는 가상 네트워크 인터페이스를 사용
* 추가적으로 docker0 브리지를 통해 veth 인터페이스와 바인딩

![](/assets/post/docker/docker-network-02.png)



* 브리지(bridge)
* 호스트(host)
* 논(none)
* 컨테이너(container)
* 오버레이(overlay)

### 네트워크 확인하기
```
docker network ls
```


```
docker network inspect bridge
```
![](/assets/post/docker/docker-network-03.png)


### 브리지 네트워크
* 컨테이너가 외부와 통신할 수 있게하는 네트워크
```
docker network create --driver bridge mybridge
```

### 호스트 네트워크
* 네트워크를 호스트로 선택하면 호스트환경의 네트워크를 컨테이너에서 사용가능
```
docker run -it --net host ubuntu:14.04
```

### 논 네트워크
* 아무 네트워크를 사용하지 않는 것
```
docker run -it --net none ubuntu:14.04
```

### 컨테이너 네트워크
* 다른 컨테이터의 네트워크를 공유해서 사용
```
docker run -it --net container:[다른 컨테이너의 ID] ubuntu:14.04
```


### 브리지 네트워크와 --net-alias
* 브리지 타입의 네트워크와 --net-alias를 함께 사용하면 특정 호스트 이름으로 여러개의 컨테이너에 접근가능
* 즉 여러개의 컨테이너를 하나의 alias이름으로 접근가능, 이때 하나의 ip를 선택해서 접근하는데 라운드 로인방식으로 결정됨
* 다음과 같이 3개의 컨테이너 생성 및 실행
```
docker run -itd --name container1 --net mybridge --net-alias forever ubuntu:14.04
docker run -itd --name container2 --net mybridge --net-alias forever ubuntu:14.04
docker run -itd --name container3 --net mybridge --net-alias forever ubuntu:14.04
```
* 테스트할 당시 172.29.0.2, 172.29.0.3, 172.29.0.4로 각각 할당된것을 다음의 명령어로 확인
```
docker inspect container1 | grep IPAddress
```
* 새로운 컨테이너를 만들고 ping을 실행
```
docker run -it --net mybridge ubuntu:14.04
root@df3d..:/# ping -c 1 forever
```
* 위에서 생성한 3개의 컨테이터에 순차적으로 ping을 보내는것을 확인할 수 있음

### MacVLAN 네트워크

## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}