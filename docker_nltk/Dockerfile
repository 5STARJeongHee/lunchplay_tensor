FROM tensorflow/tensorflow:latest-gpu-py3-jupyter

MAINTAINER LunchPlay

ADD requirements.txt .

RUN pip install --upgrade pip

RUN pip install -r requirements.txt

RUN python -c "import nltk;nltk.download('all')"

RUN mkdir /tf/LunchPlay

RUN chmod 777 /tf/LunchPlay

RUN jupyter notebook --generate-config --allow-root

ADD passwd_jupyter.sh .

EXPOSE 8888

CMD ["/bin/bash","passwd_jupyter.sh"]
