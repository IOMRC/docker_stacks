ARG cuda_version=9.0
ARG cudnn_version=7
FROM nvidia/cuda:${cuda_version}-cudnn${cudnn_version}-devel

#COPY sources.list /etc/apt/sources.list

# Install system packages
RUN apt-get update && apt-get install -y --no-install-recommends \
      bzip2 \
      g++ \
      git \
      graphviz \
      libgl1-mesa-glx \
      libhdf5-dev \
      openmpi-bin \
      vim \
      graphviz \
      openssh-client \
      rsync \ 
      module-assistant \
      wget && \
    rm -rf /var/lib/apt/lists/*

# Install conda
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH

RUN wget --quiet --no-check-certificate https://repo.continuum.io/miniconda/Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo "c59b3dd3cad550ac7596e0d599b91e75d88826db132e4146030ef471bb434e9a *Miniconda3-4.2.12-Linux-x86_64.sh" | sha256sum -c - && \
    /bin/bash /Miniconda3-4.2.12-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-4.2.12-Linux-x86_64.sh && \
    echo export PATH=$CONDA_DIR/bin:'$PATH' > /etc/profile.d/conda.sh

# Install Python packages and keras
ENV NB_USER keras
ENV NB_UID 1000

RUN useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    chown $NB_USER $CONDA_DIR -R && \
    mkdir -p /src && \
    chown $NB_USER /src

USER $NB_USER

ARG python_version=3.6

RUN conda config --append channels conda-forge
RUN conda install -y python=${python_version} && conda clean -tipsy
RUN pip install --upgrade pip && \
    pip install \
      sklearn_pandas \
      tensorflow-gpu==1.12.2 \
      cntk-gpu 
RUN conda install \
      bcolz \
      h5py \
      matplotlib \
      mkl \
      nose \
      jupyterlab \
      dask \
      distributed \
      dask-jobqueue \
      dask-drmaa \
      s3fs \
      gcsfs \
      boto3 \
      pyproj && conda clean -tipsy
RUN conda install \
      libgdal \
      owslib \
      siphon \
      Pillow \
      pandas \
      pydot \
      pygpu \
      pyyaml \
      scikit-learn \
      six \
      theano \
      xarray \
      zarr \ 
      mkdocs \
      numpy \
      scipy && conda clean -tipsy
RUN conda install \
      pyarrow \
      holoviews \
      panel \
      geoviews \
      hvplot \
      datashader && conda clean -tipsy
RUN conda install \
      cartopy \
      geopandas \
      rasterio \ 
      netcdf4 \
      h5netcdf \
      scikit-image \
      && conda clean -tipsy
RUN git clone git://github.com/keras-team/keras.git /src && pip install -e /src[tests] && \
    pip install git+git://github.com/keras-team/keras.git && \
    pip install git+https://github.com/NCAR/intake-thredds && \
    pip install jupyter-server-proxy sat-search dask-labextension && \
    conda clean -tipsy

ADD theanorc /home/keras/.theanorc

ENV LC_ALL=C.UTF-8
ENV LANG=C.UTF-8

ENV PYTHONPATH='/src/:$PYTHONPATH'

WORKDIR /data

USER root 

RUN mkdir /scratch && mkdir /group 

RUN chown $NB_USER /scratch -R && chown $NB_USER /group -R

USER $NB_USER

WORKDIR /scratch
