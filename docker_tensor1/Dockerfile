FROM tensorflow/tensorflow:1.15.4-gpu-py3-jupyter

MAINTAINER LunchPlay

ADD requirements.txt .

RUN apt-get update

RUN apt-get install -y git cmake build-essential libboost-dev libboost-system-dev libboost-filesystem-dev wget

RUN wget https://github.com/Kitware/CMake/releases/download/v3.19.1/cmake-3.19.1-Linux-x86_64.sh && chmod 777 cmake-3.19.1-Linux-x86_64.sh

RUN ./cmake-3.19.1-Linux-x86_64.sh --skip-license

RUN pip install --upgrade pip

RUN pip install jpype1

RUN pip install -r requirements.txt

RUN python -c "import nltk;nltk.download('all')"

WORKDIR /tf

RUN apt-get install -y --no-install-recommends software-properties-common

RUN add-apt-repository -y ppa:openjdk-r/ppa

RUN apt-get update

RUN apt-get install -y openjdk-8-jdk

RUN apt-get install -y openjdk-8-jre

RUN java -version

RUN mkdir /tf/LunchPlay

RUN chmod 777 /tf/LunchPlay

ADD passwd_jupyter.sh .

EXPOSE 8888

CMD ["/bin/bash","passwd_jupyter.sh"]
