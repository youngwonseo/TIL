---
layout: post
title:        "전이학습 #7 - 텍스트 문서의 범주화"
author:       "서영원"
published : false
comments: true
categories: [transferlearning]
tags: [deep learning, 딥러닝, transfer learning, 전이학습]
---

이번장에서는 전이학습을 텍스트로 구성된 문서를 분류하는 모델에 적용하는 방법을 알아봅니다. 다음은 주요 내용입니다.
* 밀집 벡터(dense vector)로 단어 표현 - 딥러닝 모델
* CNN 문서 모델 - 단어에서 문장까지 임베딩 후 문서 임베딩
* 


## 리뷰 감성 분류기

## CNN 모델로 문서 요약하기

첫 번째 단계는 각 문장에 중요도 점수를 할당해 문서의 돌출맵(saliency map)을 만드는 것

### saliency map을 만드는 단계
1. 네트워크를 통과시켜 클래스 예측
2. 네트워크의 예측을 뒤집어서 의사 레이블(pseudo label) 생성
2. 


## CNN모델을 이용한 다중 클래스 분류


## References
* [딥러닝 전이학습](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158391454&orderClick=LAG&Kc=)