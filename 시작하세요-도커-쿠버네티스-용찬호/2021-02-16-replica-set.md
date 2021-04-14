---
layout: post
title:        쿠버네티스 - 레프리카셋(Replica Set)
author:       서영원
comments: true
categories: kubernetes
---

### 레플리카셋: 일정 개수의 포드를 유지하는 컨트롤러
* 마이크로 서비스등과 같이 동일한 포드를 여러개 유지하며 서비스를 운영하는 환경에서 동일한 포드를 여러개 실행함  
* 이런 특징은 노드 장애 등과 같이 문제가 발생시 고가용성을 제공

위 같은 특징으로 포드를 직접생성하고 관리하는 일은 거의 없고 레프리카셋을 생성함으로써 포드를 관리합니다.



### replicaset-nginx.yaml
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx-pods-label
  template:
    metadata:
      name: my-nginx-pod
      labels:
        app: my-nginx-pods-label
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```


* spec.replicas : 유지할 포드 개수
* spec.selector : template항목은 포드를 정의, 이전의 포드 정의와 비슷하다는것을 알 수 있음

apply로 실행을 합니다.
```
kubectl apply -f replicaset-nginx.yaml
```

다음의 명령어를 실행하면 포드가 3개 실행되는것을 확인할 수 있습니다.
```
kubectl get pod
```
결과
```
NAME                     READY   STATUS    RESTARTS   AGE
replicaset-nginx-f2khk   1/1     Running   0          5m41s
replicaset-nginx-hvwm7   1/1     Running   0          5m41s
replicaset-nginx-xbppw   1/1     Running   0          5m41s
```

<br/>

레프리카셋과 포드는 느슨한 연결(loosely coupled)로 연결되어 있으며 이때 레플리카셋 정의에 사용했던 **label selector**를 사용합니다. 위 레플리카셋 정의파일을 살펴보면 spec.selector.matchLabels.app과 spec.template.metadata.labels.app과 동일한 my-nginx-pods-label로 설정되어 있는것을 확인할 수 있습니다. 
레플리카셋을 처음생성했을땐 app:my-nginx-pods-label의 포드가 생성되어있지 않기 때문에 template에 정의된 포드를 3개 생성하는 것입니다. **만약 실행중이 포드 하나를 삭제하면 3개로 포드를 유지하기 위해 하나의 포드를 다시 생성합니다.
이것이 레플리카셋의 역할입니다.** 

my-nginx-pods-label을 사용한 것과 같이 라벨은 쿠버네티스 리소스의 부가적인 정보를 표현할 뿐만 아니라, 서로 다은 오브젝트가 서로 찾아야 할때 사용할 수 있습니다. 

실행했던 레플리카셋은 다음의 명령어로 삭제합니다.
```
kubectl delete replicaset replicaset-nginx
```
<br/>

## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}