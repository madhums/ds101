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

FROM build as build-jupyter

# copy work files
COPY ./jupyter .
COPY ./jupyter/jupyter_notebook_config.py ${HOME}/.jupyter
COPY ./jupyter/templates ${HOME}/.jupyter

# install and enable jupyter extensions
RUN jupyter contrib nbextension install --user
RUN jupyter nbextension install --py hide_code --user
RUN jupyter labextension install @jupyterlab/toc && \
  jupyter labextension install @ryantam626/jupyterlab_code_formatter@1.3.1 && \
  jupyter labextension install jupyterlab_templates  && \
  jupyter labextension install jupyterlab-execute-time && \
  jupyter labextension install jupyterlab_filetree && \
  jupyter nbextension enable --py hide_code && \
  jupyter nbextension enable execute_time/ExecuteTime && \
  jupyter nbextension enable toc2/main && \
  jupyter nbextension enable code_prettify/autopep8 && \
  jupyter nbextension enable spellchecker/main && \
  jupyter nbextension enable ruler/main && \
  jupyter serverextension enable --py hide_code && \
  jupyter serverextension enable --py jupyterlab_code_formatter && \
  jupyter serverextension enable --py jupyterlab_templates

FROM build-jupyter

# expose port
EXPOSE 8888
