---
layout: post
title:        도커 기초 사용법
author:       서영원
published : true
comments: true
categories: docker
tags: [docker, 도커]
---
도커 사용법을 학습하기전에 도커 이미지와 컨테이너를 간략하게 알아봅시다.

## 도커 이미지와 컨테이너

[이미지와 컨테이너에 대한 그림]

### 이미지
* 컨테이너를 생성하기 위한 기본 단위
* 여러개의 계층으로 된 바이너리 파일이며 읽기전용
* 각 응용에 따라 이미지가 존재, 예로 우분투, CentOS 등과 같은 OS에 대한 이미지부터 MySql, Apache HTTP Server등과 같은 이미지가 존재

### 컨테이너
* 이미지가 실행되어 이미지에 맞는 파일시스템이 구축되고 격리된 시스템 자원 및 네트워크를 가지는 상태
* 여러개의 컨테이너를 실행하며 도커환경을 구축

<br/>

## 도커 사용하기
그럼 도커를 직접사용해 봅시다. 도커를 설치하는 방법은 [여기]를 참조해주세요. 설치가 완료되면 docker -v 명령어를 사용해 설치가 완료된것을 확인합니다. 

```
docker -v
```
제 맥에 설치된 도커 버전은 20.10.2 입니다.

<br/>

### 1. 컨테이너 생성하기
다음은 우분투 14.04버전의 이미지를 사용해 컨테이너를 실행하는 명령어 입니다. 
```
docker run -i -t ubuntu:14.04
```
ubuntu:14.04는 사용할 이미지와 이미지에 대한 버전을 위미하며 -i는 컨테이너와의 상호 입출력, -t는 tty를 활성화해서 배시(bash)를 사용하도록 하는 옵션입니다.

즉 위 명령어를 실행하면 컨테이너가 실행되고 컨테이너 내부로 접속한 것을 확인할 수 있습니다. 접속한 컨테이너에서 ls명령어를 사용하면 컨테이너 내부의 파일시스템(ubuntu)을 확인할 수 있습니다. 사용자명은 컨테이너 ID 앞의 일부분을 의미하며 저와는 다릅니다.  
![](/assets/post/docker/docker01.png)

컨테이너에 접속한것을 해제할려면 다음과 같은 방법이 있습니다.
* exit : 컨테이너 접속을 해제하는 동시에 컨테이너를 종료
* ctrl + P,Q : 접속은 해제하지만 컨테이너는 계속실행

명령어 한줄로 컨테이너를 생성하고 접근까지 하였습니다. 위 명령어에는 다음과 같은 과정이 순차적으로 실행됩니다.
1. 이미지를 다운로드 - pull
2. 컨테이너 생성 - create
3. 컨테이너 시작 - start
4. 컨테이너 내부 접속 - attach

만약 각 과정을 각각 실행하면 다음과 같이 할 수 있습니다. 위에서부터 아래로 순차적으로 실행해야 합니다.
```
docker pull ubuntu:14.04
docker create -i -t --name myubuntu ubuntu:14.04
docker start myubuntu
docker attach myubuntu
```

여기서 --name은 컨테이너에 이름을 설정에 대한 옵션입니다. 이 옵션을 지정하지 않으면 컨테이너별로 부여되는 고유 ID를 사용해 컨테이너를 시작하고 접근 할 수 있습니다.


사용할 수 있는 이미지는 [도커허브](https://hub.docker.com/){:target="_blank"}에서 검색이 가능합니다. 대부분의 응용제작사들은 각 제품에 대한 도커 이미지를 제공합니다. 또한 깃허브처럼 우리가 코드를 업로드하고 관리할 수 있듯이 이미지를 업로드하고 관리하기 위해 도커허브를 사용할 수 있습니다.


<br/>

### 2. 컨테이너 목록 확인
실행중인 컨테이너 목록은 다음과 같은 명령어로 확인할 수 있습니다.
```
docker ps
```
![](/assets/post/docker/docker02.png)

만약 위에서 ctrl+P,Q로 배쉬를 빠져나왔다면 위 그림처럼 하나의 컨테이너가 실행되고 있어야합니다. exit를 사용해서 빠져나왔다면 컨테이너가 종료되어 아무것도 안나타날것입니다.

만약 종료된 컨테이너 목록을 같이 확인하고 싶으면 다음과 같이 -a 옵션을 입력하면 됩니다.
```
docker ps -a
```

### 3. 컨테이너 종료
위 목록에 표시된 실행중인 컨테이터를 종료하고 싶으면 다음과 같이 stop명령어를 사용합니다. 이때 stop할 컨테이너의 아이디를 파라미터로 입력합니다.
```
docker stop 3e4a686ba8cc
```

### 4. 컨테이너 삭제
컨테이너를 삭제하기 위해서는 rm 명령어를 사용합니다. 마찬가지로 컨테이너 ID를 파라미터로 입력합니다.
```
docker rm 3e4a686ba8cc
```
컨테이너 삭제는 종료된 컨테이너들에 대해서만 가능합니다. 만약 실행중인 컨테이너를 삭제하고 싶으면 -f옵션을 추가하면됩니다.
```
docker rm -f 3e4a686ba8cc
```
<br/>


## 도커로 웹서버 구축하기




<br/>

## Option
다운로드된 이미지들은 images 명령어를 통해 확인가능합니다.
```
docker images
```
<br/>

## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}
* [Docker and Kubernate: The Complete Guide - Stephen Grider](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide){:target="_blank"}
