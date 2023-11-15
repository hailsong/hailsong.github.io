---
comments: true
title:  "Run 3D Gaussian Splatting (1) - Windows에서 3D 가우시안 스플래팅 구동"
excerpt: "Run 3D Gaussian Splatting on windows"

categories:
  - Study
tags:
  - Study
  - Gaussian Splating
  - Running Code
last_modified_at: 2023-11-14T012:06:01-05:00
---

### 3D Gaussian Splatting (Windows)

[Getting Started With 3D Gaussian Splatting for Windows (Beginner Tutorial)](https://www.youtube.com/watch?v=UXtuigy_wYc)

Scene Representation에서 최근 엄청난 성능 (특히 렌더링 속도 측면)을 보인 3D Gaussian Splatting에 대해서 윈도우에서 구동되는 좋은 예제가 위 링크에 소개되어 있어 정리하는 겸 한국어로 포스트를 남기게 되었습니다. Container 따로 서버에 만들고 할 필요없이 테스트 용으로 윈도우에서 학습시켜보고 싶은 분들께 좋을 것 같아요. 본 포스트 이후에는 Unity에서, 그리고 VR HMD (Meta Quest 3)에서 학습된 Gaussian 3D Splatting을 렌더링하는 자료를 포스트할 예정입니다.


<center><img src="\assets\img\3dgs-1\1.png" width="80%" height="80%"></center>

[3D Gaussian Splatting for Real-Time Radiance Field Rendering](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)

3D Gaussian Splatting은 NeRF로 대표되는 Novel View Scene Rendering과 같이 여러 사진이나 동영상 정보가 주어졌을 때 새로운 포즈에서 본 뷰를 합성하기 위한 기법으로 NeRF와 다르게 공간을 3D Gaussian 분포를 가지는 여러 Color Splat들로 보아 렌더링 시간을 획기적으로 줄인 연구입니다. 본 포스트는 코드에 대한 내용을 주로 다룰 예정입니다. 이론적인 부분은 아래 xoft님의 블로그에 논문 리뷰가 있는데 쉽게 잘 설명해주셔서 링크 걸어놓겠습니다!

[[논문 리뷰] 3D Gaussian Splatting (SIGGRAPH 2023) : 랜더링 속도/퀄리티 개선](https://xoft.tistory.com/51)

이제 아래 영상을 참고한 Windows 환경에서 3D Gaussian Splatting (이하 3DGS)를 학습시키는 레포에 대한 소개를 써보겠습니다.

[Getting Started With 3D Gaussian Splatting for Windows (Beginner Tutorial)](https://www.youtube.com/watch?v=UXtuigy_wYc)


#### Github Repositories

- [3DGS Code (Windows version)](https://github.com/jonstephens85/gaussian-splatting-Windows/tree/main) 

- [Original 3DGS Code](https://github.com/graphdeco-inria/gaussian-splatting)

-----

### Environments

제 윈도우 데스크탑 스펙은 아래와 같습니다.
- i7-8700
- RTX 3090
- 64GB RAM

GPU가 조금 중요한데, 3DGS에서 권장하는 VRAM의 크기가 20GB여서, 3090보다 안좋은 그래픽카드에서는 문제가 있을 수 있다고 합니다.

-----

### Overview

<center><img src="\assets\img\3dgs-1\2.png" width="80%" height="80%"></center>

코드는 아래와 같이 이미지를 전처리하는 부분, 3DGS를 Optimizing하는 부분, 그리고 Real-time Renderer 세 부분으로 나뉘어 있습니다.
이 중 이미지를 전처리하는 부분은 영상을 이미지 단위로 나누는 ffmpeg, 카메라 포즈를 찾고 Initial point cloud를 찾는 COLMAP이 맡게 됩니다.

-----

### Requirements

코드를 실행시키기 위해 필요한 Requirements입니다.

- Git
- Conda
- CUDA Toolkit (v11.8)
- Visual Studio (>=2019) with Desktop Development with C++
- COLMAP
- ImageMagik
- FFMPEG

#### COLMAP
[COLMAP repository](https://github.com/colmap/colmap/releases)

위 링크에서 COLMAP을 받아줍니다. 

<center><img src="\assets\img\3dgs-1\3.png" width="80%" height="80%"></center>

가장 최신 버전의 windows-cuda.zip을 다운받습니다.

<center><img src="\assets\img\3dgs-1\4.png" width="80%" height="80%"></center>

압축을 풀어서 편한 위치에 놓아줍니다. 저는 C 밑에 바로 놨습니다.

<center><img src="\assets\img\3dgs-1\5.png" width="80%" height="80%"></center>

그리고 Windows 시스템 변수의 Path에 COLMAP이 있는 경로를 추가해줍니다.

#### ImageMagik
[Download ImageMagik](https://imagemagick.org/script/download.php)

이미지 사이즈를 변화시키기 위한 프로그램입니다. 

<center><img src="\assets\img\3dgs-1\6.png" width="80%" height="80%"></center>

위 링크에서 Windoes binary release에서 최신버전 exe 파일을 받아 설치한 뒤,


#### FFmpeg
[Download FFmpeg](https://ffmpeg.org/download.html)

위 페이지에서 아래 사진들처럼 링크를 타고 들어가서 ffmpeg-git-essentials.7z를 받습니다.


<center><img src="\assets\img\3dgs-1\7.png" width="80%" height="80%"></center>


<center><img src="\assets\img\3dgs-1\8.png" width="80%" height="80%"></center>


<center><img src="\assets\img\3dgs-1\9.png" width="80%" height="80%"></center>


COLMAP과 비슷하게 시스템 변수에 해당 경로를 추가해줍니다.

<center><img src="\assets\img\3dgs-1\10.png" width="80%" height="80%"></center>


이제 Requirements 설치가 정상적으로 끝났네요!
-----


### Clone Repository

이제 코드를 둘 폴더로 가서 windows command나 git bash에서 3DGS repository를 Clone합니다.

```
# SSH
git clone git@github.com:graphdeco-inria/gaussian-splatting.git --recursive
```
or
```
# HTTPS
git clone https://github.com/graphdeco-inria/gaussian-splatting --recursive
```

-----

### Setup Conda environments

아래 절차와 같이 Conda 환경을 구성합니다.

```
# 3DGS 폴더로 이동
cd gaussian-splatting
SET DISTUTILS_USE_SDK=1 # Windows only
# environment.yml에 있는 환경대로 conda 환경 구성
conda env create --file environment.yml
# conda 환경 구동
conda activate gaussian_splatting
```

이제 3DGS구동에 필요한 라이브러리들이 gaussian_splatting conda 환경에 설치되었습니다.
여기서 뭔가 정상적으로 설치가 안되면 문제가 생길 수 있습니다. 저는 서버 문제때문인지 diff_gaussian_rasterization이 잘 안깔렸던것 같아요. 그래서 아래 이슈를 보고 해결했습니다.

[https://github.com/graphdeco-inria/gaussian-splatting/issues/444](https://github.com/graphdeco-inria/gaussian-splatting/issues/444)

혹시 모듈들이 덜깔리는 문제가 생기면 아래 방법을 써주세요!

```
pip install submodules\diff-gaussian-rasterization
pip install submodules\simple-knn
```

위 코드로 submodule들을 다시 깔아줬습니다.

-----

### 이미지 전처리

FFmpeg를 이용해 비디오를 이미지로 나누고, COLMAP을 통해 카메라 위치를 추정하는 부분입니다.

우선 복원할 씬을 담은 비디오를 촬영합니다. 저는 iphone 13으로 (4k 60fps) 제 연구실 실험실을 촬영했습니다.
30 fps로 하니까 블러가 좀 있어서 조금 문제가 있더라구요.

<center><img src="\assets\img\3dgs-1\11.png" width="80%" height="80%"></center>

초록색 크로마키가 있어서... COLMAP이 잘 작동할지 조금 걱정되긴 하네요.

이제 저장된 폴더에서 아래 command를 이용해 ffmpeg를 실행합니다.

```
ffmpeg -i {video_filename} -qscale:v 1 -qmin 1 -vf fps={fps} %04d.jpg
# 저는 아래와 같이 썼습니다.
# ffmpeg -i XR_room.MOV -qscale:v 1 -qmin 1 -vf fps=10 %04d.jpg
```

video_filename에는 동영상의 이름을, fps에는 이미지에서 몇 fps 단위로 이미지를 뽑을지 정하는 args입니다. 포스트 쓰기 전에 2FPS로 했는데 뭔가 만족스럽지 못해서 이번에는 fps를 좀 크게 잡았어요.

<center><img src="\assets\img\3dgs-1\12.png" width="80%" height="80%"></center>

아래와 같이 이미지가 4개의 정수들 이름의 jpg 파일들로 저장이 되었습니다.

이제 이 이미지들과 오리지날 비디오를 COLMAP을 위해 폴더 구성을 해줘야합니다. 저같은경우에는 gaussian-splatting 위치에 data라는 폴더를 만들고 거기에 넣었습니다.


<center><img src="\assets\img\3dgs-1\14.png" width="80%" height="80%"></center>

input 폴더 내에는 ffmpeg에서 뽑은 이미지들을 전부 넣어줬습니다.

이제 아래 command를 이용해 COLMAP을 기반으로 한 convert 파이썬 코드를 사용합니다.

```
python convert.py -s {data_path}
```

예시)
<center><img src="\assets\img\3dgs-1\15.png" width="80%" height="80%"></center>

COLMAP이 구동되는데에 시간이 조금 걸립니다..!

COLMAP 구동이 끝나면 아래와 같이 폴더가 구성됩니다.

<center><img src="\assets\img\3dgs-1\16.png" width="80%" height="80%"></center>

이제 Optimization으로 넘어가보죠.

-----

### Optimization (Traning)

아래 command를 이용해 training 코드를 실행합니다.

```
python train.py -s {data_path}
```

간단하죠?

<center><img src="\assets\img\3dgs-1\17.png" width="80%" height="80%"></center>

Training 코드에서 받는 args는 위 사진과 같습니다. 7000번째,  30000번째 iteration에서 checkpoint를 저장합니다.

<center><img src="\assets\img\3dgs-1\18.png" width="80%" height="80%"></center>

Optimizing 과정 동안 터미널에 다음 메시지들이 떴습니다.

<center><img src="\assets\img\3dgs-1\19.png" width="80%" height="80%"></center>

4k이미지를 줬었는데 (width = 3840) 가로기준 1600 넘으면 1600으로 resizing을 하니 원본 이미지를 유지하면서 인풋으로 주고싶으면 -r 옵션을 주라고 하네요.
loss로 쓰는 L1과 평가를 위한 PSNR (Peak Signal Noise Ratio)도 체크포인트를 저장할때 보여주네요.
환경이 받쳐주면 Tensorboard로도 모니터링이 가능합니다.


학습이 끝나면 gaussian-splatting/output 폴더에 저장됩니다. 여기에는 아래와 같은 파일들이 있는데요,
- input.ply # COLMAP에서 추출한 3DGS optimization에 사용된 initial point clouds
- cfg_args # 3DGS config file
- cameras.json # 이미지들의 카메라 매트릭스 정보
- point_cloud
  - iteration_7000
  - iteration_30000 # 각 iteration 안에 있는 ply파일에는 pointcloud 뿐만 아니라 splat들이 가지고 있는 color 정보나 분산 정보가 함께 들어있어 scene에 대한 표현이 한 파일 안에 있다고 볼 수 있습니다. 일반적으로 optimization 과정에서 splat들이 점점 늘어나게 되어 용량이 조금 큽니다.

-----

### Viewer

[Download Pre-built Windows Binary](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/binaries/viewers.zip)

미리 빌드된 Windows viewer의 바이너리 파일을 위 링크를 통해 다운받을 수 있습니다. 혹시 위 링크가 안된다면 - [Original 3DGS Code](https://github.com/graphdeco-inria/gaussian-splatting) 여기서 해당 내용 찾아서 다운받으면 됩니다.

저는 해당 프로그램을 gaussian-splatting/viewers에 저장했습니다.
viewers의 bin 폴더로 들어가서 다음 명령을 통해 SIBR_gaussianViewer_app.exe를 실행합니다.

```
SIBR_gaussianViewer_app.exe -m {output_folder_path}
# ex)
# SIBR_gaussianViewer_app.exe -m C:\Users\UVR_HAILSONG\Python_Projects\gaussian-splatting\output\3e66462f-6
```


<center><img src="\assets\img\3dgs-1\20.png" width="80%" height="80%"></center>

학습이 잘 됐고, 렌더링도 60FPS에 가깝게 잘 되는군요 (논문에는 더 빠르게 리포트돼있는데 옵션에서 더 빠르게 만들수 있을것 같습니다.)

Viewer 조작법은 아래와 같습니다.


Navigation in SIBR Viewers

*The SIBR interface provides several methods of navigating the scene. By default, you will be started with an FPS navigator, which you can control with W, A, S, D, Q, E for camera translation and I, K, J, L, U, O for rotation. Alternatively, you may want to use a Trackball-style navigator (select from the floating menu). You can also snap to a camera from the data set with the Snap to button or find the closest camera with Snap to closest. The floating menues also allow you to change the navigation speed. You can use the Scaling Modifier to control the size of the displayed Gaussians, or show the initial point cloud.*



cf) tensorboard 사용
```
tensorboard --logdir=./output/{# training folder...}
# ex)
# tensorboard --logdir=./output/7ef23932-f
```
output 폴더에 로그가 생겨서 텐서보드 쓰려면 디렉토리 이런식으로 설정해줘야 합니다.


다음 포스트에서는 학습시킨 3DGS를 유니티 상에서, 그리고 VR HMD인 Meta Quest 3에서 렌더링하는 과정을 공유해보도록 하겠습니다! 

---------

### References

- [3D Gaussian Splatting for Real-Time Radiance Field Rendering](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)

- [[논문 리뷰] 3D Gaussian Splatting (SIGGRAPH 2023) : 랜더링 속도/퀄리티 개선](https://xoft.tistory.com/51)

- [Getting Started With 3D Gaussian Splatting for Windows (Beginner Tutorial)](https://www.youtube.com/watch?v=UXtuigy_wYc)

- [3DGS Code (Windows version)](https://github.com/jonstephens85/gaussian-splatting-Windows/tree/main) 

- [Original 3DGS Code](https://github.com/graphdeco-inria/gaussian-splatting)




