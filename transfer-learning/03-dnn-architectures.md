# 딥러닝 아키텍처 이해하기

* 신경망 아키텍처 디자인이 중요한 이유
* 많이 쓰는 다양한 아키텍처 디자인과 그 활용


## 신경망 아키텍처
신경망 아키텍처란 신경망의 구조에 해당하는 층(layer)의 수, 유닛의 수, 레이어의 종류, 유닛이 연결되는 방법 등을 포함하는 용어입니다. 신경망의 가장 기본적인 모델인 전방 전달 다층 신경망(feedforward multilayered neural network, MLP)은 데이터의 가설공간을 학습하고 각각의 비선형 은닉층에서 복잡한 특성을 추출하는 능력이 있는데 다른 아키텍처들이 필요한 이유는 무엇일까요?

머신러닝에서 **특성 엔지니어링(feature engineering)**은 가장 중요한 작업중 하나입니다. 학습을 진행하기전 데이터를 가장 잘 표현하는 특성셋을 탐색하고 조합하는 작업으로 과소/과적 학습되는것을 방지합니다. 특성 엔지니러닝과 함께 신경망에서는 아키텍처 엔지니어링(architecture engineering)을 함께 수행합니다. 아키텍처 엔지니러닝은 데이터의 도메인 지식을 특성 엔지니어링처럼 직접적인 접근(하드코딩) 보다는 추상적인 수준으로 접근합니다. 

예를 들어 이미지분류를 위해 특징 엔지니어링으로 접근법해보면 에지/모서리 감지기, 스무딩 필터과 같은 매우 구체적인 특성을 사용해야합니다. 특히나  2차원 불변성, 즉 해당 픽셀의 데이터가 다른 픽셀로 옮겨져도 같은 분류로 표현되는 경우 모든 경우를 특성 엔지니어링으로 처리하는 것은 매우 힘든 작업입니다. 또 다른 예로 시계열 데이터의 경우 시간 순서에 따른 종속성을 가지는 데이터를 각 입력 데이터를 전부 독립적으로 처리하는 MLP로 처리할 경우 많은 유효한 정보를 학습할 수 없습니다. 

이러한 문제점들을 해결하기 위해 신경망에서는 다양한 아키텍처를 통해 특성과 함께 보다 추상적인 도메인 지식을 고려할 수 있습니다. 여기서는 신경망을 대표하는 몇 가지 구조를 살펴보겠습니다. 보다 자세한 내용은 참조되어 있는 논문을 참조하시면 됩니다.










### MLP


### 오토인코더

### 변분오토인코더
Variational autoencoders : VAEs
심층 오토인코더는 훈련이 어려움
측정 가능하고 안정적이며 효율적인 역전파, 베이즈 문석의 변분 추론의 개념에서 영감을 얻음


### 적대적 신경망
Generative Adversarial Networks : GAN

### CNN

LeNet 아키텍처
AlexNet
ZFNet
VGG
ResNet
CapsNets



### RNN
LSTM
Stacked LSTM
GRU









## References
* [](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791158391454&orderClick=LAG&Kc=)

