# 서비스 수준 gRPC 실행

## gRPC 애플리케이션 테스트

* gRPC 어플리케이션 테스트에서는 네트워크 RPC 측면도 다뤄야함

### gRPC 서버 테스트

### gRPC 클라이언트 테스트

## 배포

### 도커로의 배포

```yml
FROM golang AS build
ENV location /go/src/github.com/grpc-up-and-running/samples/ch07/grpc-docker/go
WORKDIR ${location}/server

ADD ./server ${location}/server
ADD ./proto-gen ${location}/server

RUN go get -d ./...
RUN go get -d ./...

RUN CGO_ENABLED=0 go build -o /bin/grpc-productinfo-server

FROM scratch
COPY --from=build /bin/grpc-productinfo-server /bin/grpc-productinfo-server

ENTRYPOINT ["/bin/grpc-productinfo-server"]
EXPORT 50051
```

### 쿠버네티스로의 배포

* 도커를 사용한 컨테이너 기반만으로는 확장성이나 고가용성이 보장되지 않음
* 쿠버네티스와 같은 오케스트레이션 도구를 사용하면 컨테이너관리와 확장성, 고가용성을 제공할 수 있음
* gRPC서버를 쿠버네티스로 배포하기 위해 서버 이미지에 대한 deployment와 service yaml 작성
* gRPC클라이언트를 지정 횟수만큼 실행해야 한다고 가정하면 쿠버네티스 Job을 사용
  * gRPC 서비스를 호출하고 응답을 받으면 종료하는 기능의 경우 Job을 사용
* 쿠버네티스에 실행되는 서비스를 클러스터 외부에 노출하기 위해 인그레스를 사용

## 관찰 가능성

* gRPC 어플리케이션은 일반적으로 여러개의 컨테이너로 운영되는 환경에서 배포 및 실행됨
* 여러개의 컨테이너에서 gRPC 어플리케이션은 네트워크를 통해 서로 통신
* 이때 각 컨테이를 추적하고 실제 동작하는지 확인하는 것이 관찰 가능성(observability)

* 관찰 가능성에서 세 가지 주요 항목
  * 메트릭
  * 로깅
  * 추적

### 메트릭

* 메트릭은 일정 기간동안 측정된 데이터의 숫자 표현
* 다음과 같은 주요 지표가 있음
  * 시스템 수준
    * CPU 사용량
    * 메모리 사용량
  * 어플리케이션 수준
    * 인바운드 요청률
    * 요청 에러률
* 다음과 같은 툴을 이용가능
  * [오픈센서스](https://opencensus.io/)
  * [프로메테우스](https://prometheus.io/)

### 로깅

* gRPC 어플리케이션은 인터셉터를 사용해 로깅을 활성화 가능

### 추적

* 추적은 분산 시스템을 통한 엔드투엔드 요청 흐름을 구성하는 일력의 관련 이벤트를 나타냄
* 클라이언트에서 시작한 요청은 여러 마이크로서비스를 거치는데 추적을 통해 요청이 통과한 경로와 요청 구조를 파악가능
* 추적 단위는 부산 추적의 기본 구성인 스팬 트리
  * 메타데이터, 대기시간 등이 포함

## References

* [gRPC 시작에서 운영까지](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791161754635&orderClick=LAG&Kc=)