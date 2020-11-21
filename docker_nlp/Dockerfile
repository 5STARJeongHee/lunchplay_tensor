FROM tensorflow/tensorflow:latest-gpu-py3-jupyter

MAINTAINER LunchPlay

ADD requirements.txt .

RUN pip install --upgrade pip

RUN apt-get install -y git

RUN pip install jpype1

RUN pip install -r requirements.txt

RUN python -c "import nltk;nltk.download('all')"

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

