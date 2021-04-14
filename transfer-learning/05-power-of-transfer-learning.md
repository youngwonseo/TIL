---
layout: post
title:        "전이학습 #5 - 전이학습의 위력 발휘하기"
author:       "서영원"
published : false
comments: true
categories: [transferlearning]
tags: [deep learning, 딥러닝, transfer learning, 전이학습]
---





## 전이학습의 필요성

4장에서 알아본 전이학습의 이점은 다음과 같습니다.
* 기본 성능 향상
* 전반적인 모델 개발 및 훈련 시간 단축
* 처음부터 학습하는 것보다 전반적으로 우수한 모델 성능 도출
이를 위해서는 사전 학습된 모델을 사용합니다.





딥러닝 모델을 학습하기 위한 핵심 중 한 가지는 많은 데이터와 샘플이 필요하다는 것입니다. 이미지 분류 문제를 생각해보면 


스케일링(scaling), 변환(translation), 회전(rotation) 등의 패턴과 특성을 통해 학습하기 때문제 자체적으로 특성 엔지니어링을 할 필요가 없다는 점입니다(하지만 오버피팅이 발생할 수 있습니다).




### 모델 구축 단계
다음과 같은 순서로 **개와 고양이를 분류하는 모델**을 학습하고 살펴봅니다. 각 과정을 거치면서 어떻게 과적합이 해결되는지 살펴봅시다.
1. 기본 CNN
2. Dropout이 추가된 CNN
3. 이미지 증폭
4. 사전학습된 CNN(전이학습)






https://www.tensorflow.org/tutorials/images/classification
https://www.tensorflow.org/tutorials/images/transfer_learning