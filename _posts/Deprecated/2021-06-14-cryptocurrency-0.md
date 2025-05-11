---
comments: true
title:  "Upbit 비트코인 자동거래 봇 만들기 (0) - 개요 및 개발환경 (Python, Pycharm 설치)"
excerpt: "개발 방향성, 목적"

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

 개인적인 투자를 위해 몇 가지 알고리즘 공부와 더불어 가상화폐 자동거래를 이용한 프로그래밍을 한 경험이 있다. 구글링을 통해 여러 거래소의 API를 이용하고, 정보를 얻는 과정에서 뿌듯함을 느꼈다. 포스트를 올리는 것은 정보 정리 목적도 있지만 언젠가는 이 주제로 인프런([링크](http://www.inflearn.com/))과 같은 강의 사이트에 강의를 올려보고 싶어서도 크다! 언젠가 시간내서 꼭 찍어야지 ㅎㅎ 

 예전에 개발할 때 위키북스 '파이썬을 이용한 비트코인 자동매매(개정판)'[https://wikidocs.net/book/1665](https://wikidocs.net/book/1665) 문서를 자주 참고했었습니다. 기본적인 파이썬 문법부터 간단한 PyQt, Web scrapping 기능까지 포함되어 있습니다. 다만 이 포스팅은 굉장히 핵심이 되는 API, 함수, 프로그램 구성, 기본 전략, 에러코드와 로그저장, 딥러닝 적용 가능성(RNN, LSTM, GRU etc..) 등등 매매에 꼭 필요한 부분을 다룰 예정이라 기본적인 문법은 포함하지 않겠습니다. 

------

### 포스팅 순서

0. 개요 및 개발환경 설정

1. About Upbit API
2. 함수
3. 변동성 돌파 전략
4. 프로그램 구성
5. 에러코드와 로그 저장
6. (딥러닝 적용 가능성)
7. (단타 선물 버전)

---

### 개발환경

 개발환경 세팅을 간단히 해보고자 한다. 프로그래밍 언어로는 Python을, 프로그래밍을 돕기 위한 통합 개발 환경(Integrated Development Environment, 이하 IDE)로는 Pycharm CE를 사용할 것이다. 가상환경 설정이나 기존에 파이썬을 익숙하게 다루는 경우라면 (Jupyter Notebook, VS Code, etc..) 편한 환경에서 개발하여도 무관하다.

필자는 개인적인 사정, 환경 교차 비교 겸 Windows와 MacOS 두 가지 환경에서 개발을 했다. 

**1번 개발환경**

- Windows 10
- Python 3.7.9
- Intel i7 11700K
- RTX 2060
- RAM 16GB
- IDE : Pycharm CE

**2번 개발환경**

- MacOS, Big Sur
- Macbook Air (2020)
- M1
- IDE : Pycharm CE

 프로그램이 엄청 무겁거나 하진 않으니 인터넷 연결된 어지간한 가정용 PC에서 이용하기 무리는 없을 것이다. (딥러닝 응용 전 테스크까지)

---

### 개발환경 - Python 설치

1. [python 공식 홈페이지](https://www.python.org/)에 접속한다.

2. 상단 Download 탭으로 이동한다.

   <center><img src="\assets\img\bit0_01.png" width="80%" height="80%"></center>

3. 설치하고자 하는 버전에 해당하는 숫자의 Download를 클릭한다. 필자는 3.7.9 버전을 선택했다.

   <center><img src="\assets\img\bit0_02.png" width="80%" height="80%"></center>

4. 환경에 맞는 파이썬 설치 파일을 다운로드 받는다. windows의 경우 내 컴퓨터에서 우클릭 후 속성에 들어가면 시스템 속성에 32bit/64bit 를 확인할 수 있다. (아마 대부분 64bit 환경일 것이다.) 64bit의 경우 64비트 파이썬 설치파일 (파란 밑줄), 32bit의 경우 32비트 파이썬 설치파일 (빨간 밑줄)를 클릭해 설치를 진행한다.

   MacOS의 경우 Mac pkg파일 (초록 밑줄) 을 다운로드받아 설치를 시작한다.

   <center><img src="\assets\img\bit0_03.png" width="80%" height="80%"></center>

5. 설치 파일의 안내에 따라 설치를 진행한다. **다만 Install Now를 누르기 전 add PATH ~~ 옵션을 꼭 체크해준다.** 이 옵션은 환경변수에 Python 폴더를 추가하는 옵션이다.

   <center><img src="\assets\img\bit0_04.png" width="80%" height="80%"></center>

6. 설치가 완료되면 명령 프롬프트 (검색 -> cmd)를 실행해 맞는 버전의 파이썬이 잘 설치되고 환경변수에 추가되었는지 확인한다. 

   ```
   > python -V
   Python 3.7.9
   ```

   <center><img src="\assets\img\bit0_05.png" width="30%" height="30%"></center>

   (사진의 venv는 무시해주세요!)

   위와 같이 **설치한 파이썬의 버전이 올바르게 출력되면** 정상적으로 설치가 된 것이다.

---

### 개발환경 - IDE (Pycharm CE)

 통합 개발 환경을 의미하는 IDE(Integrated Development Environment)는 변수 자동완성, 버전관리, 디버깅 등 다양한 기능을 제공해 프로그래밍을 돕는다. 가장 보편적인 IDE들 중 하나인 Pycharm Community Edition을 설치한 후 환경구성을 해보자.

1. [파이참 공식 홈페이지](https://www.jetbrains.com/ko-kr/pycharm/download/) 에 접속한다. (MacOS의 경우 Windows로 지정되어있는 탭을 macOS로 전환해준다.) Community 버전의 다운로드를 누르면 바로 설치파일(약 363MB) (.exe or .pkg)이 다운로드된다.

2. 설치 파일을 실행한다. 파이참 설치같은 경우 설치 시 딱히 건드릴만한 옵션이 없다. 중간에 환경변수(PATH)나 확장자 연결(.py)같은 옵션들을 추가해 파이참을 설치한다.

3. 파이참을 실행한 후 Create New Project를 클릭한다. 생성 창은 다음과 같이 구성되어 있다.

   <center><img src="\assets\img\bit0_06.png" width="30%" height="30%"></center>

   그림의 1에서 프로젝트를 저장할 위치를, 2에서 가상환경에 대한 설정을 하게 된다.

    여기서 가상환경에 대한 설명을 간단히 하고 넘어가고자 한다. 파이썬 프로젝트를 진행할 때에 개발자들은 다양한 종류, 다양한 종류의 모듈, 라이브러리를 import하여 사용한다. (모듈, 라이브러리는 간단히 설명하자면 특정 임무를 수행하기 위해 묶인 함수나 클래스 집단을 의미한다.) 상황에 따라 파이썬의 버전 또한 다른 버전 (2.x, 3.x) 을 사용할 수도 있다. 모듈이나 라이브러리는 프로젝트에 따라 쓰이거나 안 쓰일 수 있는데 여러 프로젝트를 진행해야 하는 상황에서 환경을 재구성하는 불편함을 방지하기 위해 파이썬은 "가상환경"을 지원한다. 가상환경은, 프로젝트별로 어떤 라이브러리를 이용하는지, 어떤 파이썬 버전을 쓰는지를 관리해주고 저장해주는 역할을 가능하게 해준다.

    어딘가에서 프로그래밍을 요리에, 가상환경 설정을 재료나 요리도구 준비에 비유한 글을 보았다. 한식을 요리할 때 칠리소스가 필요 없듯이, 양식을 요리할 때 한식 요리기구가 필요 없듯이 상황에 맞는 개발환경 구성을 편리하게 하기 위해 가상환경을 사용하고, Pycharm은 이를 효과적으로 관리할 수 있도록 돕는 기능 또한 한다.

    위의 사진으로 돌아가서, 새로운 환경을 만드려면 New environment using을, 존재하는 가상환경을 고르려면 Previsously configured interpreter에 체크를 하면 된다. 새로운 가상환경을 만들 때, base interpreter는 코드를 실행하는 파이썬으로 매칭해주면 된다. 기본 설정으로 파이썬을 설치했다면 C:/User/(사용자이름)/AppData/Local/Programs/Python/PythonXX/python.exe 와 같이 설정해주면 된다. (XX는 버전 정보)

    혹시 파이썬의 위치가 기본 위치가 아니어도 python.exe 파일을 찾아 설정해주면 된다.

    Inherit global site-pakeages는 기존 환경변수에 등록된 진짜배기 파이썬에 설치된 패키지들을 함께 불러온다는 것을, Make availavle to all projects는 새로운 프로젝트를 만들 때 Previsously configured interpreter로 불러 올 수 있도록 하는 옵션이다. 상황에 따라 유용하게 사용할 수 있다.

4. 성공적으로 프로젝트를 구성하면 다음과 같은 화면이 나타난다. (버전에 따라 예전 파이참에서는 print_hi함수가 없을 것 같다. 예전 버전에는 없었던 것 같다.)

   <center><img src="\assets\img\bit0_07.png" width="90%" height="90%"></center>

   우클릭 후 Run main을 하거나 도구모음 우측 위의 Run(초록색 삼각형 버튼)을 하거나, Ctrl + Shift + F10을 하여 main함수를 실행시켜 성공적으로 문자열이 출력되는지 확인해보자.

   <center><img src="\assets\img\bit0_08.png" width="90%" height="90%"></center>

   성공적으로 문자열이 출력되었다면, 파이참과 파이썬의 연결이 성공적으로 되었다. 자동거래 봇을 만들기 위한 개발 환경이 얼추 구성되었다!

