# lunchplay_tensor

# UBUNTU 기준 

tensorflow가 구축된 도커 이미지를 사용하려면
다음 2개가 설치되어 있어야 합니다.

1. 그래픽 드라이버

2. docker 설치

그래픽 드라이버 설치에 대해서는 버전 충돌이나 여러가지 문제가 많아서 깔끔하게 설치하기가 어려웠지만 

의외로 간단하게 드라이버를 설치하고 tensorflow docker 이미지를 사용하신 분이 있어 그분 블로그를 참고하였습니다.

https://jins-sw.tistory.com/1

--------------------------------------------------------------------------------------------

1. sudo ubuntu-drivers autoinstall

로 간단하게 드라이버를 설치할 수 있습니다. 설치 완료 후 재부팅해야 적용되므로 재부팅 해주시고


2. docker 설치는 2가지 방법이 있는데

  2-1. sudo apt install docker.io 

  로 데비안에서 제공하는 docker.io를 설치하는 방법과

  2-2. 도커에서 직접 제공하는 docker-ce를 설치하는 방법이 있습니다.(https://docs.docker.com/engine/install/ubuntu/)

    docker-ce는 다음과 같은 과정으로 설치합니다.(우분투 기준) 

    // 기존 오래된 도커 관련 패키지 삭제

    1) sudo apt-get remove docker docker-engine docker.io containerd runc  

    // docker 설치를 위해 필요한 패키지들 설치

    2) sudo apt-get update

       sudo apt-get install \
         apt-transport-https \
         ca-certificates \
         curl \
         gnupg-agent \
         software-properties-common

    // Docker 공식 GPG 키 추가

    3) curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

    //stable 버전으로 설치하도록 설정

    4) sudo add-apt-repository \
     "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) \
     stable"

    //도커 설치

    5) sudo apt-get update
       sudo apt-get install docker-ce docker-ce-cli containerd.io

    // Docker에서 GPU 컨테이너를 돌릴 수 있도록 NVIDIA Container Toolkit 설치

    6) distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
     && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
     && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee       /etc/apt/sources.list.d/nvidia-docker.list

     sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit

     sudo systemctl restart docker

     //ndivia공식문서는 조금 바뀌었는지 nvidia-container-toolkit이라 안써있고 sudo apt-get install -y nvidia-docker2 로 써져있다.
     //tensorflow 공식 사이트에서는 docker 19.03 버전 이전에는 nvidia-docker2가 설치되어 있어야하고 19.03 이상 부터는 
     //nvidia-container-toolkit을 설치하라고 되어 있다. docker --version으로 버전 확인 후 잘 선택해서 설치하자

    7) test
    docker run --gpus all nvidia/cuda:10.0-base nvidia-smi

    docker run --gpus all -it --rm -p 8888:8888 tensorflow/tensorflow:latest-gpu-py3-jupyter

    8) Dockerfile , requirements.txt 파일 작성

    컨테이너 내부를 살펴보고 필요한 라이브러리들을 더 설치한 이미지를 사용하기로 결정하여 requirements.txt에 필요한 라이브러리들을

    써두고 Dockerfile로 빌드하여 새 이미지를 만들었다. 
  
---------------------------------------------------------------------------------  

# Dockerfile 내부

### 베이스 이미지

FROM tensorflow/tensorflow:latest-gpu-py3-jupyter

MAINTAINER LunchPlay

### 필요한 종속성 파일을 기술한 파일을 컨테이너 작업 디렉토리에 옮김

ADD requirements.txt . 

### pip가 최신 버전이 아니라서 업데이트 해준다.

RUN pip install --upgrade pip

### java 와 python을 연결해주는 jpype 패키지 설치

RUN pip install jpype1

### 파이썬 라이브러리 설치

RUN pip install -r requirements.txt

### openjdk 를 설치하기 위해 저장소를 업데이트 하고 java환경 구축
RUN apt-get install -y --no-install-recommends software-properties-common

RUN add-apt-repository -y ppa:openjdk-r/ppa

RUN apt-get update

RUN apt-get install -y openjdk-8-jdk

RUN apt-get install -y openjdk-8-jre

### 주피터 노트북 사용시 생성되는 파일을 실제 로컬에서 받기 위해 마운팅용 폴더

RUN mkdir /tf/LunchPlay

### 권한 문제 방지를 위해 미리 권한 설정해둠.

RUN chmod 777 /tf/LunchPlay

--------------------------------------------------------------------------------------------------------

### docker build로 이미지 생성

Dockerfile이 위치한 경로에서 

sudo docker build -t [이미지이름]:[태그] .

위의 명령어를 통해서 도커 이미지가 생성된다. 

### 도커 이미지 도커 허브에 올리기

sudo docker login 

#위의 명령어로 docker hub에 로그인(미리 docker hub에 대한 가입을 마치고 오자)

#아래 명령어에서 태그를 달지 않으면 latest로 자동 태그가 기입됨

sudo docker tag [생성한 이미지이름]:[태그] [도커 허브 사용자 계정]/[이미지 이름]:[태그]

sudo docker push [사용자 계정]/[이미지 이름]:[태그] 



3. 만든 이미지 사용

   
  docker run --gpus all -it --rm -p [로컬 포트]:8888 -v [마운팅할 로컬 경로]:/tf/LunchPlay qhxmaoflr/lunchplay_tensor
  
  ## 주피터 실행 전에 비번 설정하도록 변경, 커맨드 창에 사용할 비번을 입력하고 웹 브라우저에 127.0.0.1:[포트] 입력하시면 됩니다.
  
  ### nlp 버전
  docker run --gpus all -it --rm -p [로컬 포트]:8888 -v [마운팅할 로컬 경로]:/tf/LunchPlay qhxmaoflr/lunchplay_tensor:nlp
  
