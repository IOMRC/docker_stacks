FROM ubuntu:18.04

LABEL maintainer="paul.branson@uwa.edu.au"

# Add local sources mirrors
COPY sources.list /etc/apt/sources.list

RUN apt-get update && apt-get install -f

RUN apt-get --no-install-recommends install -y build-essential autoconf automake gcc g++ make gfortran wget python python-dev libtool flex libstdc++6 byacc libexpat1-dev ruby build-essential wget pkg-config gedit vim git subversion uuid-dev openssh-server openssh-client\ 
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

#zlib
ENV v=1.2.8
WORKDIR /tmp/zlib
RUN wget ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-4/zlib-${v}.tar.gz \
    && tar -xf zlib-${v}.tar.gz \
    && cd zlib-${v} \
    && ./configure --prefix=/usr/local \
    && make install \
    && rm -rf /tmp/zlib

#hdf5
ENV v=1.8.13
WORKDIR /tmp/hdf5
RUN wget ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-4/hdf5-${v}.tar.gz \
    && tar -xf hdf5-${v}.tar.gz \
    && cd hdf5-${v} \
    && ./configure --enable-shared --enable-hl --prefix=/usr/local \
    && make -j 8 \
    && make install \
    && rm -rf /tmp/hdf5

#netcdf
ENV v=4.4.1
WORKDIR /tmp/netcdf
RUN wget http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-${v}.tar.gz \
    && tar -xf netcdf-${v}.tar.gz && cd netcdf-${v} \
    && ./configure --enable-netcdf-4 --enable-shared --enable-dap --prefix=/usr/local \
    && make -j 8 \
    && make install \
    && rm -rf /tmp/netcdf

#netcdf fortran
ENV v=4.4.4
WORKDIR /tmp/netcdf-fortran
RUN wget http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-fortran-${v}.tar.gz \
    && tar -xf netcdf-fortran-${v}.tar.gz \
    && cd netcdf-fortran-${v} \
    && CFLAGS="-O2" CPPFLAGS="-O2" FCFLAGS="-O2" FFLAGS="-O2" ./configure --prefix=/usr/local \
    && make -j 8 \
    && make install \
    && rm -rf /tmp/netcdf-fortran

#mpi
ENV v=4.0.2
WORKDIR /tmp/openmpi
RUN wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${v}.tar.gz
RUN tar -xzf openmpi-${v}.tar.gz \
   && cd openmpi-${v} \
   && ./configure --prefix=/usr/local \
   && make -j 8 \
   && make install \
   && rm -rf /tmp/openmpi

#xbeach
WORKDIR /tmp
RUN apt-get update && apt-get --no-install-recommends install -y curl subversion \ 
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
RUN python get-pip.py
RUN pip install mako

ADD 2018-11-09-XBeach-v1.23.5527-XBeachX_update.tar.gz /tmp
WORKDIR /tmp/2018-11-09-XBeach-v1.23.5527-XBeachX_update
RUN ./autogen.sh \
   && FCFLAGS="-O3" ./configure --prefix=/usr/local --with-netcdf --with-mpi \
   && make \
   && make install \
   && rm -rf /tmp/2018-11-09-XBeach-v1.23.5527-XBeachX_update

ENV LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH

RUN useradd -ms /bin/bash xbeach

USER xbeach

WORKDIR /

