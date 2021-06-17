---
comments: true
title:  "시계열 처리 신경망 (3) - GRU"
excerpt: "GRU의 구조와 원리"

categories:
  - Deep Learning Theory
tags:
  - deep learning
  - deep learning theory
  - time series
  - GRU
last_modified_at: 2021-06-16T08:06:00-05:00
---



### ✅ 개요

 회사 업무서 시계열 데이터를 다룰 일들이 있어 시계열 처리를 위한 신경망들을 사용했었습니다. 회사 내부적으로 AI세미나라는 이름으로 스터디를 진행하고 있는데 시계열 처리 신경망들을 발표할 일이 생겨 정리할 겸 포스트를 남겨보려 합니다.

 이 포스트는 시계열 분석 신경망 RNN, LSTM, GRU 에 대해 스터디한 내용을 정리하는 마지막 포스트로 GRU에 대해 다룹니다.

Ref : [https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/](https://ratsgo.github.io/natural%20language%20processing/2017/03/09/rnnlstm/)

ratsgo님의 블로그인데 공부하는 데에 굉장히 도움을 많이 받았습니다!

이미지 출처 : [https://colah.github.io/posts/2015-08-Understanding-LSTMs/](https://colah.github.io/posts/2015-08-Understanding-LSTMs/)

이미지 출처 2 : [https://excelsior-cjh.tistory.com/185](https://excelsior-cjh.tistory.com/185)

---

### ❓ GRU란?

<center><img src="\assets\img\dl3_01.png" width="80%" height="80%"></center>

LSTM의 구조를 조금 더 간단하게 개선한 GRU 입니다. LSTM처럼 Cell State를 사용하진 않지만 Cell state가 은닉층에 합쳐진 느낌입니다. 또한 출력층이 존재하지 않고 은닉층이 출력 정보로 그대로 나간다는 특징이 있습니다. 각 요소들을 살펴보겠습니다.

<center><img src="\assets\img\dl3_02.png" width="80%" height="80%"></center>

GRU는 이름처럼 Gate를 이용한 유닛입니다.

Update Gate는 LSTM의 Forget, Input gate를 합친 느낌으로 과거와 현재 정보 비율을 결정하고 업데이트까지 하는 역할을 합니다.

Reset Gate는 지난 정보를 얼마나 벌릴지를 결정하는 역할을 합니다. 식에서 r_t와 같이 계산하게 됩니다.

---

### 🔜 Forward Propagation (순전파)

지금부터 그린 그림들은 굉장히 수준이 떨어집니다. 양해 부탁드립니다. 😥

GRU의 전파 과정을 그린 그림이 없어 전에 공부했던 망들의 전파과정들을 따라했습니다. 혹시 틀린 부분이나 의문점이 드시면 댓글 남겨주세요!

<center><img src="\assets\img\dl3_03.png" width="80%" height="80%"></center>

 Forward Propagation은 다음과 같이 이뤄집니다. z_t가 (1-x) 연산을 거칩니다.

---

### 🔙 Backpropagation (역전파)

<center><img src="\assets\img\dl3_04.png" width="80%" height="80%"></center>

역전파는 다음과 같이 이루어져 가중치의 업데이트에 사용됩니다. (1-x) 연산의 역전파에 주의해 계산합니다.

---

### ❓ GRU..??

 공부를 하다보니 조금 다른 버전의 GRU도 찾을 수 있습니다.

<center><img src="\assets\img\dl3_05.png" width="80%" height="80%"></center>

Update Gate의 과거 현재 비율을 한번 비트는 구조로 수정한 모습입니다.

---

### 🔜 Forward Propagation (순전파)

<center><img src="\assets\img\dl3_06.png" width="80%" height="80%"></center>

---

### 🔙 Backpropagation (역전파)

<center><img src="\assets\img\dl3_07.png" width="80%" height="80%"></center>

---

이렇게 시계열 데이터를 처리하는 신경망의 유닛 RNN, LSTM, GRU에 대해 알아보았습니다. 감사합니다.