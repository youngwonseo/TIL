# 대리 분석

본 글은 [XAI 설명가능한 인공지능, 인공지능을 해부하다, (안재현 지금, 위키북스)](https://wikibook.co.kr/xai/){: target="_blank"}의 **5장 대리 분석** 내용을 요약한 것입니다.

* 대리분석(surrogate analysis)은 본래 기능을 흉내 내는 간단한 대체재를 만들어 프로토타입이 동작하는지 판단하는 분석 방법
* XAI에서는 모델이 너무 복잡해서 분석이 불가능 할때 유사한 기능을 수행하는 모델을 여러개 만들어 본래의 모델을 분석하는 방법을 의미
* 근사치 모델(approximation model), 반응 표기 기법(response surface model, RSM), 에뮬레이터(emulator)등으로 불림
* 원래 모델 f가 있고 흉내내는 모델 g가 존재 할 때, 다음 과 같은 조건에 부합되면 됨
  1. g가 f보다 학습하기 쉽고
  2. g는 설명가능한 모델
  3. g는 f를 유사하게 흉내
* 학습데이터의 전부(또는 일부)를 사용한 대리분석 모델을 글로벌 대리 분석(global surrogate analysis)
* 학습데이터 하나를 해석하는 과정을 로컬 대리 분석(local surrogate analysis)
* 대리 분석의 가장 큰 장점은 **모델 애그노스틱(model-agnostic technology, 모델에 대한 지식 없이도 학습가능)** 하다는 것
* 또한 적은 데이터로 설명가능한 모델을 만들수 있음
* 중간에 모델 f가 바뀌어도 피처만 같으면 대리 분석 수행 가능(모델들이 분리돼[decoupled] 있기 때문)


### 글로벌 대리 분석
* 글로벌 대리 분석(Global Surrogate Analysis)는 전체 학습 데이터를 사용해 블랙박스 함수 f를 따라하는 유사함수 g를 만드는 방법
  * 이때 g는 설명가능해야함
* 다음은 잘알려진 XAI 알고리즘들

|알고리즘|선형성(Linearity)|단조함수 유무(Monotone)|PDP Interaction|목표|
|------|---|---|------|---|---|
|선형 회귀|있음|단조함수|불가능|회귀|
|로지스틱 회귀|없음|단조함수|불가능|분류|
|의사 결정 트리|없음|일부|가능|분류,회귀|
|나이브 베이즈|없음|단조함수|불가능|분류|
|K-최근접 이웃|없음|단조함수 아님|불가능|분류,회귀|



* 글로벌 대리 분석 과정
  1. 데이터 집합 X 선택
  2. 1번에서 선택한 X를 사용해 블랙박스 모델 f 학습
  3. 1번에서 선택한 X를 사용해 XAI모델 선택 및 학습, 이를 모델 g
  4. f와 g의 예측 결과가 유사하도록 튜닝
* 두 모델을 비교하기 위해 R-Squared 방식을 사용한다고 했을때 다음과 같이 비교가능
  *  SSE(Sum of Square Error)와 SST(Sum of Squares Total)의 차이를 통해 근사도 측정가능
  




### 로컬 대리 분석

## LIME
* LIME(local interpretable model-agnostic explanations)은 모델이 현재 데이터의 어떤 영역을 집중해서 분석했고 어떤 영역을 분류 근거로 사용했는지 알려주는 XAI기법
* 



## 실습: 텍스트 데이터에 LIME 적용하기
* 이번 실습에서는 20개의 카테고리로 구분되어 있는 뉴스기사를 분류하는 모델을 개발하고 LIME을 적용하는 예제를 구현 

```python

# 데이터 로드 및 전처리
from sklearn.datasets import fetch_20newsgroups
newsgroups_train = fetch_20newsgroups(subset='train')
newsgroups_test = fetch_20newsgroups(subset='test')

class_names = [x.split('.')[-1] if 'misc' not in x else '.'.join(x.split('.')[-2:]) for x in newsgroups_train.target_names]
print(class_names)

class_names[3] = 'pc.hardware'
class_names[4] = 'mac.hardware'
print(class_names)

# 모델 학습 및 성능 평가
import sklearn
import sklearn.metrics
from sklearn.naive_bayes import MultinomialNB

vectorizer = sklearn.feature_extraction.text.TfidfVectorizer(lowercase=False)
train_vectors = vectorizer.fit_transform(newsgroups_train.data)
test_vectors = vectorizer.transform(newsgroups_test.data)

nb = MultinomialNB(alpha=.01)
nb.fit(train_vectors, newsgroups_train.target)

pred = nb.predict(test_vectors)
sklearn.metrics.f1_score(newsgroups_test.target, pred, average='weighted')
```
