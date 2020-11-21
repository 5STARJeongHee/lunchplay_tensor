FROM tensorflow/tensorflow:1.15.4-gpu-py3-jupyter

MAINTAINER LunchPlay

ADD requirements.txt .

RUN apt-get install -y git

RUN pip install --upgrade pip

RUN pip install jpype1

RUN pip install -r requirements.txt

RUN python -c "import nltk;nltk.download('all')"

RUN pip uninstall -y tensorflow tensorboard tensorflow-estimator tensorflow-gpu 

RUN pip install tensorflow-gpu==1.15.4 keras==2.3.1

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
