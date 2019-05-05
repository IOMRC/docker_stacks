FROM jupyter/scipy-notebook
ARG JUPYTERHUB_VERSION=0.9.6
#RUN conda install --no-cache \
#    jupyterhub==$JUPYTERHUB_VERSION

USER root 

COPY sources.list /etc/apt/sources.list

RUN apt-get update \
    && apt-get -yq install man-db acl openssh-client rsync openssh-client rsync module-assistant graphviz sshfs fuse python-fuse curl gnupg2\
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN curl https://rclone.org/install.sh | bash

RUN mkdir  /home/$NB_USER/shared-data
RUN chown $NB_USER /home/$NB_USER/shared-data
RUN chmod g+rws /home/$NB_USER/shared-data
RUN setfacl -d -m g::rwx /home/$NB_USER/shared-data

#RUN export GCSFUSE_REPO=gcsfuse-`lsb_release -c -s`
#RUN echo "deb http://packages.cloud.google.com/apt gcsfuse-bionic main" | sudo tee /etc/apt/sources.list.d/gcsfuse.list && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - &&  apt-get update && apt-get -yq install gcsfuse

USER $NB_USER

RUN pip install --no-cache jupyter-server-proxy

COPY environment.yml environment.yml

RUN conda env create -f environment.yml

# Pull the environment name out of the environment.yml
RUN echo "source activate $(head -1 environment.yml | cut -d' ' -f2)" > ~/.bashrc
ENV PATH /opt/conda/envs/$(head -1 environment.yml | cut -d' ' -f2)/bin:$PATH

RUN conda clean -tipsy

#RUN pip install jupyter-server-proxy 
#RUN pip install --upgrade nbconvert notebook nbserverproxy
#RUN jupyter serverextension install jupyter-server-proxy
#RUN jupyter serverextension enable --py jupyter-server-proxy --sys-prefix
RUN jupyter labextension install @jupyter-widgets/jupyterlab-manager@0.38 \
                             @jupyterlab/hub-extension \
                             @pyviz/jupyterlab_pyviz@0.6.4 \
                             jupyter-leaflet@0.8.1 

#RUN jupyter serverextension enable --sys-prefix --py nbserverproxy 
RUN jupyter serverextension enable --sys-prefix jupyterlab 

RUN pip install git+https://github.com/NCAR/intake-thredds

RUN echo /home/$NB_USER/.config/dask/
RUN mkdir /home/$NB_USER/.config/dask/

COPY distributed.yaml /home/$NB_USER/.config/dask/

USER root

RUN echo "deb http://packages.cloud.google.com/apt gcsfuse-bionic main" | sudo tee /etc/apt/sources.list.d/gcsfuse.list && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - &&  apt-get update && apt-get -yq install gcsfuse && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir  /home/$NB_USER/imos
RUN chown $NB_USER /home/$NB_USER/imos
RUN chmod g+rws /home/$NB_USER/imos
RUN setfacl -d -m g::rwx /home/$NB_USER/imos

USER $NB_USER
RUN mkdir /home/$NB_USER/.config/rclone
RUN chown $NB_USER /home/$NB_USER/.config/rclone
RUN chmod g+rws /home/$NB_USER/.config/rclone
RUN setfacl -d -m g::rwx /home/$NB_USER/.config/rclone
COPY --chown=jovyan:users rclone.conf /home/$NB_USER/.config/rclone
RUN chmod g+rws /home/$NB_USER/.config/rclone/rclone.conf

RUN mkdir /home/$NB_USER/.aws
RUN chown $NB_USER /home/$NB_USER/.aws
#COPY --chown=jovyan:users aws_credentials /home/$NB_USER/.aws/credentials

USER $NB_USER

RUN conda install nb_conda_kernels

#RUN conda install jupyter-server-proxy

#RUN conda install h5netcdf

#RUN conda install bokeh netcdf4

#RUN jupyter labextension install jupyterlab_bokeh 


#ENV PATH=$PATH:/usr/local/go/bin
#RUN curl -O https://storage.googleapis.com/golang/go1.8.3.linux-amd64.tar.gz && \
#    tar -C /usr/local -xzf go1.8.3.linux-amd64.tar.gz && \
#    rm go1.8.3.linux-amd64.tar.gz

#RUN curl -L -O https://github.com/kahing/catfs/releases/download/v0.4.0/catfs && \
#    mv catfs /usr/bin && chmod 0755 /usr/bin/catfs

#RUN curl -L -O https://bit.ly/goofys-latest && \
#    mv goofys-latest /usr/bin/goofys && chmod 0755 /usr/bin/goofys

#USER $NB_USER
#ENV GOOFYS_CACHE="--cache /tmp/cache -o allow_other"
#ENV ENDPOINT="http://s3.ap-southeast-2.amazonaws.com/"
#ENV BUCKET="imos-data"
#ENV GOOFYS_ENDPOINT="--endpoint $ENDPOINT"
#ENV GOOFYS="goofys -f --stat-cache-ttl 1s --type-cache-ttl 1s ${GOOFYS_CACHE} ${GOOFYS_ENDPOINT} $BUCKET /home/jovyan/imos"

#USER root

#RUN apt-get update \
#    && apt-get -yq install nco netcdf-bin \
#    && apt-get clean \
#    && rm -rf /var/lib/apt/lists/*

#USER $NB_USER

