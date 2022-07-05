---
comments: true
title:  "Run SCANimate (CVPR 2021)"
excerpt: "Run SCANimate Demo code"

categories:
  - Avatar
tags:
  - Avatar
  - Deep Learning
last_modified_at: 2022-07-05T012:06:01-05:00
---



## SCANimate

SCANimate: Weakly Supervised Learning of Skinned Clothed Avatar Networks (CVPR 2021 Oral)

[SCANimate Project Page](https://scanimate.is.tue.mpg.de/)



SCANimate는 3D Human model generation에 대해 깊고 다양한 연구를 하는 Max-Planck-Gesellschaft 에서 연구된 논문으로 Weakly Supervised Learning을 통해 구멍이나 부족한 부분이 있는 여러 Raw Scan Data를 통해 얻은 Input Data로 옷을 포함한 상태의 Animatable 한 full body avatar를 만드는 내용의 논문이다.

model의 main figure는 다음과 같다.

<center><img src="\assets\img\scanimate_1\1.png" width="80%" height="80%"></center>

----

## SCANimate Demo Code

[SCANimate Github](https://github.com/shunsukesaito/SCANimate)

SCANimate Github page에서는 raw scan data들로 논문에서 제시하는 human 3d model인 1) scanimat을 만드는 코드와 2) pose 데이터를 이용해 frame별 자세를 만드는 코드, 그리고 3) Rendering하여 보여주는 코드를 제공하고 있다. Github에서 제공하는 코드를 실행시키는 과정을 정리했다.

개발 환경 : Ubuntu 18.04, CUDA10.1, Conda

(CUDA 10.0짜리 container로하다가 다시 했다...)



- Installation.txt

  ```
  1. Setup virtual environment:
  Go to the scanimate directory in the command line, then
  $ source ./install.sh
  If you use other python and cuda versions (default python3.6 cuda 10.1), please change the cuda version and python version in ./install.sh
  
  2. Download the smpl model:
  Download smpl models from https://smpl.is.tue.mpg.de/, put them into models folder under ./scanimate/smpl/models/smpl
  By default we use 10 PCA models and .pkl format.
  ```

- install.sh

  ```
  #/bin/bash
  echo "Set up virtualenv for scanimate..."
  virtualenv -p python3.6 ./venv/scanimate
  . ./venv/scanimate/bin/activate
  
  echo "Installing torch..."
  echo "If you are using other versions (default python3.6 cuda 10.1), change the cuda version and python version in ./install.sh"
  pip install torch==1.7.1+cu101 torchvision==0.8.2+cu101 torchaudio==0.7.2 -f https://download.pytorch.org/whl/torch_stable.html
  
  echo "Installing pytorch3d..."
  echo "If you are using other versions (default python3.6 cuda 10.1), change the cuda version and python version in ./install.sh"
  pip install pytorch3d -f https://dl.fbaipublicfiles.com/pytorch3d/packaging/wheels/py36_cu101_pyt171/download.html
  
  echo "Installing other dependencies..."
  pip install -r requirements.txt
  
  echo "Installing customized smpl code"
  cd smpl
  python3 setup.py install
  cd ../
  
  echo "Done!"
  ```

- requirement.txt

  ```
  numpy==1.19.5
  trimesh==3.9.20
  matplotlib==3.3.4
  scikit-image==0.17.2
  pyyaml==5.4.1
  chumpy==0.70
  tqdm==4.61.1
  ```

  

코드에서 제공하는 설치 방법은 installation.txt에 따르면 install.sh를 통해 설치하라고 되어있는데 이전에 smplify-x를 설치할 때에 pytorch3d 설치 과정에서 문제를 겪었던 과정을 생각하여 dependency가 안꼬이길 바라면서 virtualenv가 아닌 conda를 사용하여 가상환경을 구축하였다.



```
conda create -n smplify-x python=3.6
conda activate smplify-x
```



pytorch3d를 막 설치하다보니까 

ImportError: /home/dlituiev/.conda/envs/torch3d/lib/python3.7/site-packages/pytorch3d/_C.cpython-37m-x86_64-linux-gnu.so: undefined symbol: _ZN3c104impl23ExcludeDispatchKeyGuardC1ENS_11DispatchKeyE

와 같은 Error가 있었는데 

[https://github.com/facebookresearch/pytorch3d/issues/495](https://github.com/facebookresearch/pytorch3d/issues/495)

를 참조해 해결했다.

install.sh를 바로 실행하지 말고

```
conda install -c pytorch pytorch=1.7.0 torchvision cudatoolkit=10.1
conda install -c conda-forge -c iopath fvcore iopath
conda install -c pytorch3d pytorch3d
```

를 이용해 conda로 pytorch3d를 설치해줬다.

pytorch3d를 설치한 뒤에는 뒤쪽 나머지 dependency를 설치해줬다.



```
#echo "Installing other dependencies..."
pip install -r requirements.txt

#echo "Installing customized smpl code"
cd smpl
python3 setup.py install
cd ../

echo "Done!"
```



그리고 아래 안내에 따라 필요한 모델들을 다운로드받아준다.

**1. First download the pretrained model, some motion sequences and other files for the demo**

- Download an AIST++ dance motion sequence for test (CC BY 4.0 license):

```
$ . ./download_aist_demo_motion.sh
```

 This script will create a `data` folder under current directory, please make sure to put it under the `SCANimate` directory.

- Download pre-trained scanimats for animation test: Please visit https://scanimate.is.tue.mpg.de/download.php, register, login, read and agree to the license and then download some demo scanimats. Unzip the zip file into `./data` directory
- Download subset of CAPE data for training demo: Please visit https://scanimate.is.tue.mpg.de/download.php, register, login, read and agree to the license and then download the data for training demo. Unzip the zip file into `./data` directory.
- Now you should have a `./data` directory under `SCANimate`. Within `./data` you will have 5 directories: `minimal_body`, `pretrained`, `pretrained_configs`, `test`, and `train`.



또한 smpl 모델 파일들을 넣어줘야한다.

Download the smpl model:
Download smpl models from https://smpl.is.tue.mpg.de/, put them into models folder under ./scanimate/smpl/models/smpl
By default we use 10 PCA models and .pkl format.

.pkl파일 두개를 넣어줘야하는데 이때 파일 이름을 SMPL_FEMALE.pkl, SMPL_MAKE.pkl로 바꿔줘야한다. 이런식으로!

<center><img src="\assets\img\scanimate_1\2.png" width="80%" height="80%"></center>





그 다음으로 Run animation demos에 나온대로 따라하면 된다.

----

### Run animation demos:

**2. Now you can run the test demo with the following command:**

```
$ python -m apps.test_scanimate -c ./data/pretrained_configs/release_03223_shortlong.yaml -t ./data/test/gLO_sBM_cAll_d14_mLO1_ch05
```

- You can replace the configuration file with other files under `./data/pretrained_configs/` to try other subjects.
- You can also replace the test motions with others under `./data/test`.
- The result will be generated under `./demo_result/results_test`.

**3. The generated mesh sequences can be rendered with the code under `./demo_result`**:

First, install Open3D (for rendering the results) by:

```
$ pip install open3d==0.12.0
```

Then run:

```
$ python render/render_aist.py -i demo_result/results_test/release_03223_shortlong_test_gLO_sBM_cAll_d14_mLO1_c
```



apps.test_scanimate를 실행할 때 몇 가지 오류가 났는데 아래에 정리해놨다.

$ python -m apps.test_scanimate -c ./data/pretrained_configs/release_03223_shortlong.yaml -t ./data/test/gLO_sBM_cAll_d14_mLO1_ch05 부분을 통과하면 ./demo_result/results_test에 애니메이션을 만들기 위한 각 프레임이 생긴다.

<center><img src="\assets\img\scanimate_1\3.png" width="80%" height="80%"></center>



$ python render/render_aist.py -i demo_result/results_test/release_03223_shortlong_test_gLO_sBM_cAll_d14_mLO1_c

open3d를 설치해서 rendering하는 과정에서 GUI를 통해 보여주면서 rendering을 하게 되는데... 이게 문제가 연구실 서버에 깔린 리눅스로 작업을 하다 보니 디스플레이장치가 없어 보여주질 못해 오류가 생기는 것 같았다. 

GLFW Error: X11: The DISPLAY environment variable is missing

사실 FM대로라면 [https://github.com/adioshun/gitBook_PCL/issues/1#issuecomment-1065382119](https://github.com/adioshun/gitBook_PCL/issues/1#issuecomment-1065382119)의 첫 문단에서 제시하는것과 같이 스크린을 X server로 forward해서 보여주는게 맞겠지만 귀찮아서 open3d사용하는 코드부터는 결과를 연구실 윈도우 컴퓨터로 가져와서 돌렸다.



<center><img src="\assets\img\scanimate_1\4.png" width="80%" height="80%"></center>

코드 뒷부분에 ffmpeg랑 rm을 사용하는 부분이 있는데 아래 블로그를 보면서 ffmpeg를 해결했고, rm부분은 중간에 동영상 연결하면서 남는 이미지 파일에 대한것인데 크게 중요하지 않아서 주석처리했다.

[https://www.lainyzine.com/ko/article/how-to-install-ffmpeg-on-windows-10/](https://www.lainyzine.com/ko/article/how-to-install-ffmpeg-on-windows-10/)

[https://www.lainyzine.com/ko/article/how-to-install-package-manager-chocolatey-on-windows-10/](https://www.lainyzine.com/ko/article/how-to-install-package-manager-chocolatey-on-windows-10/)



----

### 설치 시 만났던 오류 troubleshooting

- [OSError: libusb-1.0.so: cannot open shared object file: No such file or directory while building docs in readthedocs](https://stackoverflow.com/questions/60648478/oserror-libusb-1-0-so-cannot-open-shared-object-file-no-such-file-or-director)

  ```
  sudo apt-get -y install libusb-1.0-0-dev
  ```

  로 해결

- [ImportError: libGL.so.1: cannot open shared object file: No such file or directory](https://www.inflearn.com/questions/57362)

  ```
  sudo apt-get install libgl1-mesa-glx
  ```

  opencv import하는 과정에서 발생했던 오류

  

------

### Output

<center><img src="\assets\img\scanimate_1\3.png" width="80%" height="80%"></center>

각 프레임별로 obj 파일이 이런식으로 나왔고

<center><img src="\assets\img\scanimate_1\5.gif" width="80%" height="80%"></center>

요런식으로 랜더링이 됐다.

-----

### Future Work

논문에서는 texture에 대한 내용이 있었는데 코드에는 포함되어있지 않은 것 같았다.

texture에 대해 reference삼은 아래 논문들을 확인해봐야할 것 같다.

Jingwei Huang, Justus Thies, Angela Dai, Abhijit Kundu,
Chiyu Max Jiang, Leonidas J. Guibas, Matthias Nießner, and
Thomas A. Funkhouser. Adversarial texture optimization
from RGB-D scans. In 2020 IEEE/CVF Conference on
Computer Vision and Pattern Recognition, CVPR 2020,
Seattle, WA, USA, June 13-19, 2020, pages 1556–1565. IEEE,
2020

Michael Oechsle, Lars M. Mescheder, Michael Niemeyer,
Thilo Strauss, and Andreas Geiger. Texture fields: Learning
texture representations in function space. In 2019 IEEE/CVF
International Conference on Computer Vision, ICCV 2019,
Seoul, Korea (South), October 27 - November 2, 2019, pages
4530–4539. IEEE, 2019.

Shunsuke Saito, Zeng Huang, Ryota Natsume, Shigeo
Morishima, Hao Li, and Angjoo Kanazawa. PIFu: Pixelaligned
implicit function for high-resolution clothed human
digitization. In 2019 IEEE/CVF International Conference on
Computer Vision, ICCV 2019, Seoul, Korea (South), October
27 - November 2, 2019, pages 2304–2314. IEEE, 2019. 3



사족으로 mpg에서 제공해준 input raw scan data가 생각보다 깔끔해서 결과가 잘 나오는게 아닌가 라는 생각이 들었다. 

<center><img src="\assets\img\scanimate_1\6.gif" width="80%" height="80%"></center>

손 부분만 구멍이 나고 이런식으로 거의 되있어서 나중에 scan data를 직접 획득한걸로 데모해볼 수 있으면 해볼 필요가 있겠다.

---------

### Reference

[SCANimate Project Page](https://scanimate.is.tue.mpg.de/)

[SCANimate Github](https://github.com/shunsukesaito/SCANimate)

