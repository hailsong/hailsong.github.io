---
comments: true
title:  "Run SMPLify-X"
excerpt: "Run SMPLify-X Demo code"

categories:
  - Avatar
tags:
  - Avatar
  - Deep Learning
last_modified_at: 2022-06-30T012:06:01-05:00
---



## SMPLify-X demo code

돌리는데에 시행착오가 조금 있었어서 저같은 상황에서 참조하실 분들이 계실까봐 포스트로 남깁니다...

개발환경은 ubuntu18.04, cuda 10.0 이었다.

[https://github.com/vchoutas/smplify-x/issues/3#issuecomment-507682801](https://github.com/vchoutas/smplify-x/issues/3#issuecomment-507682801)



[https://colab.research.google.com/github/sergeyprokudin/smplpix/blob/main/colab_notebooks/Convert_Video_to_SMPLpix_Dataset.ipynb](https://colab.research.google.com/github/sergeyprokudin/smplpix/blob/main/colab_notebooks/Convert_Video_to_SMPLpix_Dataset.ipynb)



첫번째 링크에서 conda로 환경구성하는데에 오류가 있다는 이슈가 있다는 이야기가 있어서 따라 해봤는데 결과가 좋지 않았다.

두번째 링크는 SMPLpix라는 SMPL모델을 Neural rendering하는 논문(자세히는 아직 보지 못했다.)의 과정에서 SMPLify-x를 쓰는 코드가 colab으로 제공되어 참조했다! 별 기대안하고 돌렸는데 colab에서 성공해버릴 줄이야...



colab코드에서는

1) video 업로드 및 ffmpeg를 이용한 video에서의 프레임 추출
2) Openpose를 이용한 각 프레임에서의 2D body keypoint 추출
3) 추출한 keypoint와 이미지를 이용해 SMPLify-x 실행

의 순서로 코드가 진행되어 있었다.



1, 3번은 개발중이던 cuda, ubuntu 버전에서 정상적으로 진행할 수 있었는데 openpose를 빌드하는 과정에서(아마 CUDA version때문에) 오류가 있어서 획득한 프레임에 colab에서 실행시켜서 얻은 2d body keypoint의 .json파일을 받아 실행하기로 했다. (목적은 SMPLify-x를 돌리는 것이니)



### 1. 프레임 추출

!ffmpeg -i "$VIDEO_PATH" -vf fps=$FPS -qscale:v 2 '$FRAMES_DIR/%05d.png'

ffmpeg를 이용해 쉽게 비디오에서 프레임들을 추출할 수 있었다.

참고로 이 글에 표시한 과정을 하려면 어차피 colab에서 프레임을 추출하는 과정이 있어 거기서 따와도 전혀 무방하다

참조 : ffmpeg documentation

https://ffmpeg.org/ffmpeg.html



### 2. Openpose 이용 2D body keypoint 추출

사실 굳이 colab에서 openpose를 돌리는 이유는 열심히 smplify-x 세팅을 해놨는데 dependency가 깨질 것만 같아서 무서워서였다... 위에 링크해둔 colab을 차례대로 실행시키다가

<center><img src="\assets\img\smplify-x1\1.png" width="80%" height="80%"></center>

이 부분을 지나면 keypoint들이 저장된다.

ls로 저장되있는 파일들을 확인했다.

<center><img src="\assets\img\smplify-x1\2.png" width="80%" height="80%"></center>

```python
!cd openpose && ./build/examples/openpose/openpose.bin --image_dir $FRAMES_DIR --write_json $KEYPOINTS_DIR --face --hand --display 0   --write_images $OPENPOSE_IMAGES_DIR

```

openpose를 실행시킨 명령어인데 아마 앞에서의

KEYPOINTS_DIR = os.path.join(RES_DIR, 'keypoints')

OPENPOSE_IMAGES_DIR = os.path.join(RES_DIR, 'openpose_images')

이 폴더 안에 openpose의 output이 저장되는 듯해 이를 다운로드 받았다.

<center><img src="\assets\img\smplify-x1\3.png" width="80%" height="80%"></center>

<center><img src="\assets\img\smplify-x1\4.png" width="80%" height="80%"></center>

다운로드된 keypoint와 rendered image 확인



### 3. RUN SMPLify-x!

크고작은 troubleshooting 과정이 있었다... 최대한 정리하면서 하려고 했는데 아래에 정리했다.

패키지 하나하나 깔때마다 dependency 깨질까봐 엄청 걱정했는데 다행히 삐걱대지만 잘 돌아간다.



colab에 있던 smplify-x 하고 dependency 설치과정은 아래와 같았다.

conda 환경 설정

```
conda create -n smplify-x python=3.7
conda activate smplify-x
```

smplify-x / dependency 설치과정

```python
# @title Install SMPLify-X and other dependencies

%cd /content
!pip install chumpy
!pip install smplx
!git clone https://github.com/vchoutas/smplx
%cd smplx
!python setup.py install

#vposer
!pip install git+https://github.com/nghorbani/configer
!pip install git+https://github.com/sergeyprokudin/human_body_prior

# !pip install torch==1.1.0   이 부분은 패스했다.
%cd /content
!git clone https://github.com/sergeyprokudin/smplify-x
%cd /content/smplify-x
!pip install -r requirements.txt
```

여기서 torch부분을 뺐는데 1.1.0버전에 대한 지원이 안된 점도 있었고 이미 smplx설치 과정에서 dependency로 torch 최신버전이 깔려서 패스해줬다.



코드 실행시키기 전에 smplify model하고 vposer 파일을 넣어줘야한다. smplify 공식 사이트에서 받을 수 있다. https://smpl-x.is.tue.mpg.de/



대망의 main.py 실행

```
python smplifyx/main.py --config cfg_files/fit_smplx.yaml --data_folder ./smplifyx/data --output_folder ./smplifyx/output --visualize=False --gender='male' --model_folder ./smplifyx/models/models --vposer_ckpt ./smplifyx/vposer/vposer_v1_0
```

./smplifyx/data 폴더에 1, 2번 과정에서 나온 images, keypoints들을 폴더를 만들어 정리해줘야 한다. 

--gender에서 성별 옵션 주면 되고 --model_folder, --vposer_ckpt에 다운받은 model하고 vposer 저장 위치를 넣어줘야 한다.

근데 이러고 끝은 아니었고... 많은 힘든 과정들이 아래와 같이 있었다.

dotmap 관련

https://copypaste.guru/WhereIsMyPythonModule/how-to-fix-modulenotfounderror-no-module-named-dotmap

torch penetration 설정

https://github.com/vchoutas/smplify-x/issues/9

issue에서는 torch-mesh-isect를 따로 설치하라고 했으나 아래 방법처럼 간단하게 penetration 옵션을 끄는걸로 타협

<center><img src="\assets\img\smplify-x1\5.png" width="80%" height="80%"></center>

torch 내부 subtract 표기 문제

https://stackoverflow.com/questions/65637222/runtimeerror-subtraction-the-operator-with-a-bool-tensor-is-not-supported

Library "GLU" not found.

https://data-newbie.tistory.com/710

Display 관련 오류?

<center><img src="\assets\img\smplify-x1\6.png" width="80%" height="80%"></center>

Display 관련된 오류같은데... 정확히 어떤것때문에 문제가 생긴지는 모르겠지만 pyglet이나 render관련된 내용이 있는걸로 봐서 visualize를 따로 해주는 거에 대한 문제인 것 같다. 이 문제가 있을 때 visualization에 True 옵션을 주면 프레임 한개만 준 상태에서 프로그램이 끝나버려서 나는 False옵션을 주었다. 내 생각에 ubuntu desktop환경에서 돌렸다면 여기서 부딪히지 않았을 것 같다.

다만 False 옵션을 주었더니 Output으로 Render된 모습은 따로 보여주지 않는다.



------

### Output

<center><img src="\assets\img\smplify-x1\7.png" width="80%" height="80%"></center>

다음과 같은 구조로 Output이 나온다. images에는 아마 사진에 겹쳐서 랜더한 결과가 나오지 않았을까 싶은데 visualization에 False옵션을 줘서 따로 나오지 않았다.

meshes에는 각 프레임별로 .obj 결과로, results에는 .pkl파일로 결과물이 나온다.

**성공 Case**

알기 쉬운 자세에서는 mesh가 input이미지랑 비슷한 형태로 잘 나왔다.

<center><img src="\assets\img\smplify-x1\8.png" width="80%" height="80%"></center>

**실패 Case**

하지만 blur가 있거나 꼬인 위치에서는 잘 맞지 않는 모습을 보였다.

<center><img src="\assets\img\smplify-x1\9.png" width="80%" height="80%"></center>



-----

### Future Work

몇몇 결과물에서 texture도 포함된 Output을 보여줬던 것 같은데 그렇지 못해서 body model 복원 method 중 texture정보를 포함한 다른 코드들을 돌려보며 확인해봐야할 것 같다.

워낙 빠르게 발전하는 분야이다 보니까 SMPLify-X보다 상대적으로 최근 모델들을 돌리면서 비교해봐야할 것 같다.

RGB-D Video Input의 SCANimate를 다음에는 돌려볼 수 있을 것 같다.

중간중간 troubleshooting과정에서 몇 가지 term (특히 penetration 옵션)을 누락시킬수밖에 없었는데 결과에 악영향을 끼쳤을 것 같다는 생각이 든다. 지금 연구실 server 상황에서 적용 가능한 방법들을 생각해봐야겠다.

mesh output을 겹쳐서 render해준 사진도 나왔으면 좋았겠는데 하는 아쉬움이 남는다.



---------

### Reference

SMPLify-X Github : https://github.com/vchoutas/smplify-x#visualizing-results

SMPLify-X Paper : https://ps.is.tuebingen.mpg.de/uploads_file/attachment/attachment/497/SMPL-X.pdf

SMPLify-X Project Page : https://smpl-x.is.tue.mpg.de/

SMPLpix Github : https://github.com/sergeyprokudin/smplpix

SMPLpix에서 제공해준 notebook파일로부터 정말 많은 도움을 받았다!





