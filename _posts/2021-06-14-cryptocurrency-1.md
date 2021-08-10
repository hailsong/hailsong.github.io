---
comments: true
title:  "Upbit 비트코인 자동거래 봇 만들기 (1) - About Upbit API"
excerpt: "업비트 API 발급 및 라이브러리 설치. 함수 살펴보기"

categories:
  - Bitcoin Auto Trading
tags:
  - code
  - upbit
  - auto-trading
  - bitcoin
last_modified_at: 2021-06-14T08:06:00-05:00
---



### 개요

 업비트의 파이썬 API에서는 가격 조회, 호가창 조회, 이전 가격 조회 등 로그인이 계정과 무관한 함수와 보유 코인 정보, 매수, 매도 등 개인 계정과 관련된 함수가 함께 제공된다. 본 포스트에서는 개발환경에 API 설치하는 법, 업비트 API를 사용하기 위한 등록절차, 간단한 함수들에 대해 다루겠다.

------

### 라이브러리 설치 (pyupbit)

 파이썬을 설치한 후 환경변수에 파이썬 경로를 추가하게 되면 pip 명령어를 사용할 수 있게 된다. 개발을 하고자 하는 가상환경을 activate하고 다음 명령어로 pyupbit를 설치해준다.

```sh
pip install pyupbit
```

