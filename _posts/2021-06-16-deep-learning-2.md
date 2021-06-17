---
comments: true
title:  "시계열 처리 신경망 (2) - LSTM"
excerpt: "LSTM의 구조와 원리"

categories:
  - Deep Learning Theory
tags:
  - deep learning
  - deep learning theory
  - time series
  - LSTM
last_modified_at: 2021-06-16T08:06:00-05:00
---



### ✅ 개요

 회사 업무서 시계열 데이터를 다룰 일들이 있어 시계열 처리를 위한 신경망들을 사용했었습니다. 회사 내부적으로 AI세미나라는 이름으로 스터디를 진행하고 있는데 시계열 처리 신경망들을 발표할 일이 생겨 정리할 겸 포스트를 남겨보려 합니다.

 이 포스트는 시계열 분석 신경망 RNN, LSTM, GRU 에 대해 스터디한 내용을 정리하는 두 번째 포스트로 LSTM에 대해 다룹니다.

Ref : [https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/](https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/)

ratsgo님의 블로그인데 공부하는 데에 굉장히 도움을 많이 받았습니다!

이미지 출처 : [https://colah.github.io/posts/2015-08-Understanding-LSTMs/](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)



---

### ❗ RNN의 한계

 앞선 포스팅에서 RNN에 대해 알아보았는데 이런 RNN에는 치명적인 한계가 있습니다. 무엇일까요?

<center><img src="\assets\img\dl2_01.png" width="80%" height="80%"></center>

  정답은 장기 의존성 문제입니다. RNN의 경우 정보와 그 정보를 사용하는 지점 사이가 가까운 경우 문제가 없지만 정보와 사용 지점의 거리 차이가 크면 학습 능력이 저하되는 장기 의존성 문제가 발생하게 됩니다. LSTM은 오래된 정보가 사라지는 것을 방지하기 위해 만들어진 신경망입니다.

---

### ❓ LSTM이란?

<center><img src="\assets\img\dl2_02.png" width="80%" height="80%"></center>

 RNN보다 조금 더 복잡해진 LSTM의 구조입니다. 위쪽 신경망이 이전 포스트에서 설명드린 RNN, 아래쪽의 신경망이 LSTM입니다. LSTM은 Long Short Term Memory의 약자입니다. Input Gate의 노드들 사이에는 RNN에서 사용한 tanh 말고도 sigmoid함수도 사용됩니다. 

 LSTM은 어떻게 장기 의존성 문제를 회피할까요?

<center><img src="\assets\img\dl2_03.png" width="80%" height="80%"></center>

 LSTM은 Cell state라는 구조를 통해 장기 의존성 문제를 피합니다. Cell State는 어떤 정보를 기억할지, 어떤 정보를 버릴지에 대한 데이터를 기억합니다.

이전 은닉층의 데이터와 이번 시퀀스의 input 데이터는 합쳐져 네 갈래로 복제됩니다. 이 중 첫번째 갈래는 sigmoid 함수를 지나 Cell state로 들어가 이전 Cell state 정보를 기억할지 안할지를 결정합니다. 이 부분이 Cell state와 합쳐지는 상황에서 X표시가 되어있는데 0~1사이의 output과 Cell State의 기존 데이터가 요소별로 곱해져 이전 cell state를 기억할지 버릴지를 정하게 됩니다. 이 X표시 Gate를 Forget Gate라고 부릅니다.

<center><img src="\assets\img\dl2_04.png" width="80%" height="80%"></center>

복제된 네 갈래 중 두번째 세번째 갈래는 각각 시그모이드와 hyperbolic tangent함수를 거쳐 요소곱을 하게 되고 이 데이터는 Cell state에 concatenate됩니다. Concatenate는 더해지는것과 달리 기존 데이터 매트릭스 옆으로 정보를 붙이는 것을 의미합니다. 시퀀스를 지날때마다 Cell state에 concatenate되니까 이 Cell State는 점점 크기가 커지게 되는 것이지요.

<center><img src="\assets\img\dl2_05.png" width="80%" height="80%"></center>

복제된 마지막 데이터는 sigmoid함수를 거쳐 Cell State정보와 요소곱을 해 output이 되고 다음 은닉층으로 향하게 됩니다.

---

### 🔜 Forward Propagation (순전파)

<center><img src="\assets\img\dl2_06.png" width="80%" height="80%"></center>

 Forward Propagation은 다음과 같이 이뤄집니다. 중간에 H_t가 네 갈래로 나뉘는 것을 다른 문자로 나누었음을 확인합니다. 활성함수가 차이가 있음에 유의합니다.

---

### 🔙 Backpropagation (역전파)

<center><img src="\assets\img\dl2_07.png" width="80%" height="80%"></center>

역전파는 다음과 같이 이루어져 가중치의 업데이트에 사용됩니다. dH_t는 4갈래로 나뉜 엣지들의 로컬 그라디언트들의 합으로 계산됨에 유의합니다.