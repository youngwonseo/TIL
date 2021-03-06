# 머신러닝의 기초 원리

![https://miro.medium.com/max/1400/1*-oVnNlSFTUxvpJ42VIVpRw.jpeg]()





머신러닝 기법

사람의 개입에 따른 범주화
지도 학습 : 입력과 출력 사이의 연관성을 매핑해 학습, 즉 학습할 데이터가 (x,y)로 구성되었음, 이것은 사람의 개입이 많이 들거간다는 것을 의미. 분류와 회귀 등
비지도 학습 : 입력과 출력의 연관이 아닌 입력 데이터에 내재된 잠재적 구저와 패턴, 관계를 학습. 군집화, 차원 축소, 연관 규칭 마이닝 등
준지도 학습 : 지도 학습과 비지도 학습을 혼합한것. 레이블이 부탁된것과 그렇지 않은 데이터를 같이 학습
강화 학습 : 에이전트가 환경과 상호작용하며 보상을 최대로 하는 정책을 학습
데이터 가용성에 따른 범주화
배치 학습(오프라인 학습): 학습이 일괄적으로 수행된 후 배포
온라인 학습: 데이터가 미니 배치로 시스템에 입력되어 계속해서 학습을 진행




## 지도 학습
### 분류
로지스틱 회귀, 서포트 벡터 머신(SVM), 신경망, 랜덤 포레스트, k-최근접 이웃(KNN), 의사결정 트리 등이 포함

### 회귀
선형 회귀, 다변량 회귀, 회귀 트리 등이 포함

## 비지도 학습

### 군집화

중심화 기반 방법 : K-mean, K-medoid
응집과 분리의 계층적 군집화 방법: 와드(Ward)와 친근도 천파(affinity propagation)
데이터 분포 기반 방법: 가우스 혼합 모델
밀도 기반 방법: DBSCAN 등
### 차원 축소
주성분 분석(PCA), 최근접 이웃, 판별 분석

### 연관 규칙 마이닝
FP0성장, ECLAT, Apriori 등

### 이상탐지



## CRISP-DM
데이터 마이닝에서 교차 산업 표준 프로세스(Cross Industry Standard Process for Data Mining, CRISP-DM)는 데이터 마이닝 및 분석 프로젝트에 가장 널리 사용되는 프로세스 중 하나
비즈니스 요구사항 공식화부터 데이터에서 통찰력을 얻기 위한 솔루션 테스트에서 배포까지 프로젝트를 실행하데 필요한 단계, 프로세스, 워크플로 등을 명확히 보여줌
데이터 과학은 반복적으로 데이터로부터 의미있는 통찰력과 정보를 얻으려고함
데이터 과학은 인문학이라고 말할 수 있음, 이유없이 알고리즘을 반복해서 실행하는 것이 아닌 사업에 대한 이해, 투자하는 노력의 실제 가치, 그리고 최종 결과와 통찰력을 표현하는 적절한 벙법같은 노력이 포함되기 때문에
CRIPS-DM은 데이터 마이닝 및 분석 프로젝트를 End-to-End로 추적하는데 도움을 줌

![https://www.ibm.com/support/knowledgecenter/ko/SS3RA7_sub/modeler_crispdm_ddita/clementine/images/crisp_process.jpg]()

### CRISP-DM의 6단계
비즈니스 이해
데이터 이해
데이터 준비
모델링
평가
배포


## 표준 머신러닝 워크플로우
CRISP-DM 모델은 머신러닝과 관련 프로젝트 관리를 위한 높은 수준의 워크플로우를 제공합니다. end-to-end로 동작하며 다음과 같이 표현됩니다.
[그림]


데이터 검색: 
데이터 준비: 전체 파이프라인에서 가장 많은 시간을 소모하는 단계, 다음과 같은 세부 단계를 포함
탐색적 데이터 분석(Exploratory Data Analysis, EDA): 실제 수집된 데이터를 처음으로 깊게 탐구하는 단계, 데이터의 다양한 측면을 이해하는 단계, 데이터의 여러 속성을 분석하고 시각화도 진행, 파이프라인 포기에 잠재적인 문제를 식별
데이터 처리와 데이터 랭글링: 데이터를 사용가능하도록 변환, 누락된 데이터 첨가, 형변환, 중복 항목 처리, 이상값 처리 등이 포함
특성 엔지니어링과 특성 추출: 데이터에 존재하는 특성을 이용하여 머신러닝 알고리즘에서 사용할 수 있도록 상황별 특성을 추출
특성 스케일링과 특성 선택: 데이터의 특성이 너무 많아 일부를 선택, 이를 통해 차원의 저주를 회피
모델링: 모델에 데이터를 입력하고 훈련하는 단계, 하나 또는 그 이상의 머신러닝 알고리즘을 사용, 여러 알고리즘이나 기법을 사용한 다음 모델 평가를 통해 모델을 선택
모델 평가와 평가: 
교차 검증을 통해 하이퍼파라미터 튜닝
지도학습 평가: 
TP, FP, TN, FN 등의 지표
정확도, 정밀도, 재현율, F1점수등을 포함하는 Confusion matrix
ROC(receiver operator characteristic)곡선과 AUC(area under curve)
R-제곱, 평균 제곱근 오차(RMSE), F-분포, AIC(Akaike information criterion), 회귀모델의 p-값
군집화 평가
실루엣 계수
제곱 평균 오차
동차성과 완전성, V-측정
칼린스키-하라바츠(Calinski-Harabaz) 지수
편향-분산 트레이드 오프


모델 튜닝
배포와 모니터링



## 특성 추출과 특성 엔지니어링


## References