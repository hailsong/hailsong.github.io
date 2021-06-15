---
comments: true
title:  "시계열 처리 신경망 (1) - RNN"
excerpt: "RNN의 구조와 원리"

categories:
  - Deep Learning Theory
tags:
  - deep learning
  - deep learning theory
  - time series
  - RNN
last_modified_at: 2021-06-15T08:06:00-05:00
---



### 개요

 회사 업무서 시계열 데이터를 다룰 일들이 있어 시계열 처리를 위한 신경망들을 사용했었습니다. 회사 내부적으로 AI세미나라는 이름으로 스터디를 진행하고 있는데 시계열 처리 신경망들을 발표할 일이 생겨 정리할 겸 포스트를 남겨보려 합니다.

 본 포스트를 시작으로 RNN, LSTM, GRU 신경망에 대한 포스트를 업로드할 예정입니다. 발표를 위한 자료를 재정리한 정보라 순서가 조금 어색할 수 있습니다.

Ref : [https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/](https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/)

ratsgo님의 블로그인데 공부하는 데에 굉장히 도움을 많이 받았습니다!

이미지 출처 : [https://colah.github.io/posts/2015-08-Understanding-LSTMs/](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)



------

### RNN 개요

 시계열 데이터를 다룬 이유는 손의 동작을 카메라로 인식하기 위해서였습니다. 정지한 손의 모양은 한 프레임의 사진만이 필요하지만 손을 돌리거나, 움직이는 행위를 판단하기 위해서는 여러 프레임의 사진이 필요했습니다. 이와 같은 상황에서는 일반적인 신경망이 아닌 시계열 데이터를 처리하기 위한 신경망을 사용하는 것이 좋습니다. 자연어 처리, 날씨 예측 등의 시계열 데이터 처리를 위한 신경망에는 RNN, LSTM, GRU 등이 있습니다.

---

### REMIND! (Basic DL)

<center><img src="\assets\img\dl1_01.png" width="80%" height="80%"></center>

 AI는 인간의 뉴런을 모방한 프로그램적인 뉴런인 퍼셉트론들의 구성들로 층을 이뤄 배치한 것을 의미합니다. 우리 뉴런이 입력신호를 받아 신경세포를 거쳐 축삭돌기로 전기신호를 내 다음 뉴런으로 신호를 전달하듯이, 퍼셉트론은 이전 퍼셉트론으로부터의 입력값을 받아 각각의 가중치를 곱해 활성함수를 통해 다음 퍼셉트론으로 값을 전달하거나 예측값을 전달합니다. 딥러닝은 신경망으로부터 나온 값을 실제 값과 일치시키기 위해서 각각의 입력에 곱해지는 가중치와 bias를 업데이트하고 우리는 이 과정을 학습이라고 부릅니다. 예측값과 실제값의 차이를 이용해 가중치를 업데이트하는 과정에서 계산이 반대 방향으로 순차적으로 이루어지는데 이를 back propagation 또는 역전파라고 부릅니다.

활성 함수에는 오른쪽 아래와 같이 시그모이드 함수, 하이퍼볼릭탄젠트, ReLU 등이 사용됩니다.

---

### RNN이란?

<center><img src="\assets\img\dl1_02.png" width="80%" height="80%"></center>

 RNN은 Recurrent Neural Networks의 약자로 은닉층이 순차적으로 순환구조를 이루는 인공 신경망의 한 종류를 뜻합니다. 아래 그림은 RNN 네트워크의 동작 과정을 나타내는 그림입니다. T번째 시퀀스에 해당하는 변수값들에 아래첨자로 t라고 표시했습니다. T번째 시퀀스에서는 t번째 시퀀스에 해당하는 input뿐 아니라, 이전 시퀀스의 은닉층 노드에 해당하는 h_(t-1)을 받아 가중치 W_hh를 곱한 값 또한 받아 활성함수 하이퍼볼릭탄젠트에 들어가게 됩니다. 다음 시퀀스인 t+1번째 시퀀스에서도 마찬가지로 이전 시퀀스의 은닉층 노드의 값 h_t를 이용해 계산하게 됩니다. 이와 같이 RNN은 시계열 데이터에 대한 처리가 가능합니다. 

---

### Forward Propagation (순전파)

<center><img src="\assets\img\dl1_03.png" width="80%" height="80%"></center>

 Forward Propagation은 다음과 같이 이뤄집니다. 이전 시퀀스의 출력 계층이 아닌 은닉층에서 다음 은닉층으로 정보가 전달됨에 유의합니다.

---

### REMIND! (Backpropagation)

<center><img src="\assets\img\dl1_04.png" width="80%" height="80%"></center>

(오타냈다... 왜 일관성없게 순전파는 forward propagation이고 역전파는 backpropagation일까...)

신경망에서 어떤 역할을 하는 신경망을 만들기 위해 가중치를 최적화 할 때, 엔지니어들은 예측값과 실제 값을 차이를 이용합니다. 예측값과 실제값의 차이를 나타내는 LOSS function을 이용해 앞의 가중치들을 업데이트 해야하는데 여기에는 경사하강법이 사용됩니다. 경사하강법은 loss funtion이 줄어드는 방향으로 가중치를 업데이트하는 방법론으로 오차를 뒤로 전달하며 오차의 기울기값인 라운드L라운드x값을 chain rule을 이용하여 구해 이용합니다.

---

### Backpropagation (역전파)

역전파는 다음과 같이 이루어져 가중치의 업데이트에 사용된다.

<center><img src="\assets\img\dl1_05.png" width="80%" height="80%"></center>