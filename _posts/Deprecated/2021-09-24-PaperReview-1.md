---
comments: true
title:  "논문 읽기(1) - Body2Hands: Learning to Infer 3D Hands from Conversational Gesture Body Dynamics (CVPR 2021)"
excerpt: "논문 읽기,  Body2Hands: Learning to Infer 3D Hands from Conversational Gesture Body Dynamics (CVPR 2021)"

categories:
  - paper review
tags:
  - deep learning
  - hand gesture
  - GAN
  - CVPR
last_modified_at: 2021-09-24T012:06:00-05:00
---



### 논문 읽기

 근본없이 필요한 부분만 공부하고 사용하는 모습이 무서워져 논문 읽기 연습을 조금씩 해보는 겸 정리를 해보고자 한다! 무지한 기계과 학부 졸업생이 나름대로 읽은 내용을 정리하는 것인지라 많이 부족할텐데 혹시 잘못되거나 표현이 이상한 부분이 있다면 피드백 환영합니다. 😁  

---



### 개요

![img](http://people.eecs.berkeley.edu/~evonne_ng/projects/body2hands/images/teaser.png)

> **Body2Hands: Learning to Infer 3D Hands from Conversational Gesture Body Dynamics (CVPR 2021)** 를 참조해 작성한 포스트입니다.
>
> ref : <a href="http://people.eecs.berkeley.edu/~evonne_ng/projects/body2hands/">http://people.eecs.berkeley.edu/~evonne_ng/projects/body2hands/<a>



hand gesture를 분석할 때의 주된 세 가지 challenges는 occlusions, motion blur, low resolution입니다. 확실한 이미지를 얻을 수 없는 상황에서 손 외의 body pose가 hand pose와 연관되어 있다는 직관에서 시작한 논문으로 GAN을 이용해 **Body Pose만으로 손의 모양을 만들어냅니다.** (optional하게 손 이미지를 받을 수도 있습니다.) 특히 손의 이미지가 확실하게 잡히지 않는 상황에서 body2hands는 그 진가를 발휘하게 됩니다.  

<center><img src="\assets\img\paper1_01.png" width="80%" height="80%">
<figcaption>손의 이미지가 깔끔하지 못한 예시</figcaption></center>
  

네트워크 구조는 다음 그림과 같습니다.  



<center><img src="\assets\img\paper1_02.png" width="80%" height="80%">
<figcaption>네트워크 구조</figcaption></center>

Optional Input로 손의 이미지를 받아 Resnet으로 들어가지만 주된 input은 Body pose input입니다. body pose는 Body pose encoder, UNet, Hand pose decoder를 통해 Hand Gesture를 만들어내고(Generator 역할) 이는 L1 Loss로 평가됩니다. 또한 추측한 Hand Gesture는 discriminator로 진위 여부를 판별하는 GAN 구조를 가집니다.

> 참조 
>
> <a href="https://idiotdeveloper.com/what-is-unet/">What is UNet?<a>
>
> <a href="<a href="https://idiotdeveloper.com/what-is-unet/">What is UNet?<a>">Overview of GAN Structure<a>

<center><img src="\assets\img\paper1_03.png" width="40%" height="40%">
</center>

(1)의 g는 Body Pose(B)를 받아 손의 모양(H)를 출력하는 Generator로 (2) 식과 같이 L1 Loss로 평가됩니다. Dis

