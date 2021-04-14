# 의사 결정 트리

본 글은 [XAI 설명가능한 인공지능, 인공지능을 해부하다, (안재현 지금, 위키북스)](https://wikibook.co.kr/xai/){: target="_blank"}의 **4장 의사 결정 트리** 내용을 요약한 것입니다.

## 의사결정트리
* 의사결정트리는 질의과정이 트리형태(브런치로 분기가되는)로 나타나는 분류(classification) 또는 예측(prediction)을 위한 분석방법(지도학습)
* 답을 찾아가는 과정을 도식적으로 표현가능하여 연구자가 분석 과정을 쉽게 이해하고 설명할 수 있다는 장점이 있음
* 일반적으로 의사결정 트리는 정보 이득 수치를 계산해 최적 목표를 달성, 즉 주어진 데이터를 잘 설명하는 트리를 만들기 위해 정보 이득을 이용하는데 이것은 엔트로피 변화량을 의미
![](엔트로피 수식)

* 정보 이득, 즉 엔트로피량이 커지면 획득할 정보 이득의 양이 커진다는 것을 의미
* 의사결정트리는 다양한 분류방식을 시도하고 정보 이득량이 가장 커지는 방향으로 트리를 학습, 이를 재귀적 분기(recursive partitioning) 하고 함
* 모든 학습 데이터에 대해 100% 분류가되는 트리를 풀 트리(full tree)라고 함, 이는 과적합(overffiting)의 가능성이 있음
* 이를 방지하기 위해 특정 임계값보다 낮은 수준의 정보 이득을 발생시키는 가지를 잘라냄, 이를 프루닝(prunning)이라 함
* 의사결정트리에 대한 자세한 설명은 위키피디아나 다른 머신러닝 서적 참고 필요

## 의사 결정 트리 시각화

## 피처 중요도 구하기
* 피처 중요도(feature importance, 또는 퍼뮤테이션 중요도[permutation importance])는 데이터의 피처가 알고리즘의 정확한 분류에 얼마나 큰 영향을 미치는지, 즉 피처가 알고리즘을 학습하는데 있어서 얼마나 중요한지 분석하는 기법
* 특정 피처값을 임의의 값으로 변경했을때 에러가 얼마나 증가하는지 측정, 만약 에러가 크게 증가하는 경우 해당 알고리즘은 변경한 피처에 의존하여 결정을 하는것으로 판단 가능
* 만약 피처의 값을 변경했는데 모델 성능이 변화가 없다면 그 피처는 무시 가능
* 이러한 피처 중요도를 계산하는 한 가지 방법인 피셔, 루딘, 도미니치의 피터 중요도 계산 방법
  1. 주어진 모델의 에러를 측정
  2. X의 피처 k개에 대하여
      1. 피처 매트릭스 X를 계산, X는 각 피처를 임의의 값으로 변경한 모델
      2. X의 모델 에러를 측정, 변경된 피처값을 사용하여 에러측정
      3. 퍼뮤테이션 피처 중요도를 산정, 각 k의 F1 = permutation error / origin error
  3. 피처의 중요도 F1을 계산



## XGBoost 
* XGBoost(eXtreme Gradient Boosting)는 캐글에서 다수의 우승을 한 의사결정트리로 사용하기 쉽고 CPU만으로도 학습가능한 알고리즘
* 배깅(Bagging), 부스팅(Boostring), 스태킹(Stacking)을 활용
* XGBoost는 딥러닝은 아님
* 피처가 많지 않다면 



### 기본 원리

### 파라미터

https://gist.github.com/ktisha/c21e73a1bd1700294ef790c56c8aec1f

1. Number of times pregnant
2. Plasma glucose concentration a 2 hours in an oral glucose tolerance test
3. Diastolic blood pressure (mm Hg)
4. Triceps skin fold thickness (mm)
5. 2-Hour serum insulin (mu U/ml)
6. Body mass index (weight in kg/(height in m)^2)
7. Diabetes pedigree function
8. Age (years)
9. Class variable (0 or 1)


![](/public/xai/04/04_01.PNG)

* 루트 노드를 살펴보면 f1이라고 명시되어 있는데 이것은 두 번째 피처인 혈장 포도당 농도를 의미, 즉 이 수치가 127.5 미만 또는 값이 없거나(missing) 그렇지 않을 경우를 기준으로 분기가 실행됨
* 그후 f7(나이) 또는 f5(BMI 수치)로 분기가 발생
* leaf노드의 값은 로지스틱 함수 확률값(logistic function probability score)으로 0은 중립, 음수는 당뇨병 없음, 양수는 당뇨병이 있다고 분류한것을 의미(sigmoid(0.5) == 0), 즉 0.5를 임계값으로 당뇨병이 있다, 없다를 구분
* XGBoost의 max_depth값을 조절하여 다음과 같이 프루닝된 트리 생성가능
![](/public/xai/04/04_02.PNG)
* 이처럼 의사결정트리를 시각화하면 모델이 어떤 피처를 우선순위로 생각하는지, 단계마다 어떤 기준으로 당뇨병을 진단하는지 순서대로 파악 가능


### 피처 중요도
* 피처들 간의 상관관계가 낮아야함, 즉 3개의 피처인 키, 몸무게, BMI는 키나 몸무게의 변화에 따라 변화하므로 피처 중요도가 높게 설정될 수 있음
* 피처 중요도는 피처 간 의존성이 낮은 상태에서 효율이 높음
* 다음은 피처 중요도를 출력하는 코드 및 결과

```python
from xgboost import plot_importance

rcParams['figure.figsize'] = [10,10]
plot_importance(model)
plt.show()

```
![](/public/xai/04/04_03.PNG)
* 결과는 중요도 준으로 정렬되어 있음
* 피처 중요도 순으로 꼭 의사결정트리의 순서가 결정되는 것은 아님, 트리는 정보 이득이 큰 방향으로 학습됨
* 그러나 대부분 피처 중요도와 의사결정트리의 순서가 동일함


### 부분 의존성 플롯

![](/public/xai/04/04_04.PNG)


* 다음은 데이터와 실제 모델을 결합해 두 결과가 일치하는지 확인하는 **예측 분포 플롯(prediction distribution plot)**
![](/public/xai/04/04_05.PNG)



## 모델 튜닝

## References
* [XAI 설명가능한 인공지능, 인공지능을 해부하다, (안재현 지금, 위키북스)](https://wikibook.co.kr/xai/){: target="_blank"}
* 