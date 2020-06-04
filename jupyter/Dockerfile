FROM jupyter/scipy-notebook as scipy


# ===================
# Jupyter
# ===================

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

FROM scipy as build-scipy

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

FROM build-scipy as build-jupyter


# ===================
# Open Data Cube
# https://hub.docker.com/r/opendatacube/datacube-core/dockerfile
# ===================

COPY ./jupyter/datacube.conf ${HOME}

USER root

# Install apt dependencies, including gdal, netcdf and hdf5
RUN apt-get install -y g++ \
  libhdf5-serial-dev \
  libnetcdf-dev \
  hdf5-tools \
  netcdf-bin \
  gdal-bin \
  gdal-data \
  libgdal-dev \
  libudunits2-dev

ENV CPLUS_INCLUDE_PATH=/usr/include/gdal
ENV C_INCLUDE_PATH=/usr/include/gdal
RUN pip install GDAL==$(gdal-config --version)

# Get ODC and install it
ENV APPDIR=/tmp/code
RUN mkdir -p $APPDIR
WORKDIR $APPDIR
RUN git config --global url."http://".insteadOf https://
RUN git clone https://github.com/opendatacube/datacube-core.git .
RUN pip install netCDF4 rasterio h5py
RUN pip install -e .

# Set the locale, this is required for some of the Python packages
ENV LC_ALL C.UTF-8

# Remove installation code
RUN rm -rf ${APPDIR}

# Fix an issue with libcurl shipped in rasterio
RUN mkdir -p /etc/pki/tls/certs \
  && ln -s /etc/ssl/certs/ca-certificates.crt /etc/pki/tls/certs/ca-bundle.crt;

USER $NB_USER

FROM build-jupyter as build-scipy-jupyter-odc

WORKDIR /notebooks

# expose port
EXPOSE 8888
