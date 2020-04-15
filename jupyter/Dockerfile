FROM jupyter/scipy-notebook as build

WORKDIR /notebooks

# copy requirements.txt
COPY jupyter/requirements.txt .

RUN pip install -r requirements.txt --src ${HOME}
RUN python -m nltk.downloader punkt stopwords

# install postgres bindings
USER root
RUN apt-get update
RUN apt-get install libpq-dev -y
USER $NB_USER

FROM build

# copy work files
COPY ./jupyter .

# install and enable jupyter extensions
RUN jupyter contrib nbextension install --user
RUN jupyter nbextension install --py hide_code --user 
RUN jupyter nbextension enable --py hide_code && \
  jupyter serverextension enable --py hide_code && \
  jupyter nbextension enable execute_time/ExecuteTime && \
  jupyter nbextension enable toc2/main && \
  jupyter nbextension enable code_prettify/autopep8 && \
  jupyter nbextension enable spellchecker/main && \
  jupyter nbextension enable ruler/main

# expose port
EXPOSE 8888
