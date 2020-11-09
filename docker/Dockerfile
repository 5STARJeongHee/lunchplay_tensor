FROM tensorflow/tensorflow:latest-gpu-py3-jupyter
MAINTAINER LunchPlay
ADD requirements.txt .
RUN pip install --upgrade pip
RUN pip install Kaggler
RUN mkdir /tf/LunchPlay
RUN chmod 777 /tf/LunchPlay
RUN jupyter notebook --generate-config --allow-root
#RUN echo "c.NotebookApp.password = 'hello'" >> /root/.jupyter/jupyter_notebook_config.py
ADD passwd_jupyter.sh .

EXPOSE 8888

CMD ["/bin/bash","passwd_jupyter.sh"]
