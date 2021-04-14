---
layout: post
title:        쿠버네티스 - 디플로이먼트(Deployment)
author:       서영원
comments: true
published : true
categories: kubernetes
---

### 디플로이먼트: 레플리카셋, 포드의 배포를 관리
* 실제 운영환경에서 레플리카셋을 yaml파일에 정의해서 사용하는 경우는 거의 없고 디플로이먼트 오브젝트 정의를 통해 레플리카셋을 정의합니다. 즉 레플리카셋에 포드가 정의된것같이 [디플로이먼트]->[레플리카셋]->[포드] 순으로 오브젝트의 상하위 구조를 가집니다.


### deployment-nginx.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      name: my-nginx-pod
      labels:
        app: my-nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```


```
kubectl apply -f deployment-nginx.yaml
```

## 디플로이먼트 사용이유
레플리카셋만으로 포드가 유지될 수 있지만 **어플리케이션의 업데이트와 배포**를 더욱 편리하게 하기 위해서 디플로이먼트를 사용합니다. 
* 어플리케이션을 업데이트할 경우 레플리카셋의 변경 사항을 저장하는 리비전(revision)을 남겨 롤백을 가능하게 함
* 포드의 롤링 업데이트 전략을 통해 무중단 서비스를 가능하게 함 



--record옵션
```
kubestl apply -f deployment-nginx.yaml --record
```


이미지 버전 변경
```
kubectl set image deplyment my-nginx-deployment nginx=nginx:1.11 --record
```


레플리카셋 확인


리비전 확인
kubectl rollout history deployment my-nginx-deployment


kubectl rollout undo deployment my-nginx-deployment --to-revision=1



모든 실습 후에는 다음의 명령어로 리소스 정리를 수행합니다.
```
kubectl delete deployment,pod,rs --all
```





## References
* [시작하세요! 도커/쿠버네티스 - 용찬호](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158392291&orderClick=LEa&Kc=){:target="_blank"}