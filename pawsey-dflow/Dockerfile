FROM pawsey/mpi-base:latest
#FROM ubuntu:18.04

LABEL maintainer="paul.branson@uwa.edu.au"

COPY sources.list /etc/apt/sources.list

#RUN apt-get update \
#    && apt-get install -y autoconf automake gcc g++ make gfortran wget python python-dev libtool flex libstdc++6 byacc libexpat1-dev uuid-dev ruby build-essential wget pkg-config gedit vim git subversion  \
#    && apt-get clean all \
#    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -f

RUN apt-get --no-install-recommends install -y build-essential autoconf automake gcc g++ make gfortran wget python python-dev libtool flex libstdc++6 byacc libexpat1-dev ruby build-essential wget pkg-config gedit vim git subversion uuid-dev \ 
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

### Build MPICH ###
#RUN mkdir /tmp/mpich-build
#WORKDIR /tmp/mpich-build
#ENV MPICH_VERSION=3.1.4
#ENV MPICH_CONFIGURE_OPTIONS=--disable-option-checking --prefix=/usr --enable-fast=all,O3 --cache-file=/dev/null --srcdir=. CC=gcc CFLAGS="-DNDEBUG -DNVALGRIND -O3" LDFLAGS="LIBS=-lpthread" CPPFLAGS="-I/tmp/mpich-build/mpich-3.1.4/src/mpl/include -I/tmp/mpich-build/mpich-3.1.4/src/mpl/include -I/tmp/mpich-build/mpich-3.1.4/src/openpa/src -I/tmp/mpich-build/mpich-3.1.4/src/openpa/src -D_REENTRANT -I/tmp/mpich-build/mpich-3.1.4/src/mpi/romio/include"
#ENV MPICH_MAKE_OPTIONS=-j8 
#RUN wget http://www.mpich.org/static/downloads/${MPICH_VERSION}/mpich-${MPICH_VERSION}.tar.gz \
#    && tar xvzf mpich-${MPICH_VERSION}.tar.gz 
#RUN cd mpich-${MPICH_VERSION}  \
#    && ./configure --disable-option-checking --prefix=/usr --enable-fast=all,O3 --cache-file=/dev/null --srcdir=. CC=gcc CFLAGS="-DNDEBUG -DNVALGRIND -O3" LDFLAGS="" LIBS=-lpthread CPPFLAGS="-I/tmp/mpich-build/mpich-${MPICH_VERSION}/src/mpl/include -I/tmp/mpich-build/mpich-${MPICH_VERSION}/src/mpl/include -I/tmp/mpich-build/mpich-${MPICH_VERSION}/src/openpa/src -I/tmp/mpich-build/mpich-${MPICH_VERSION}/src/openpa/src -D_REENTRANT -I/tmp/mpich-build/mpich-${MPICH_VERSION}/src/mpi/romio/include" \
#    && make ${MPICH_MAKE_OPTIONS} \
#    && make install \
#    && ldconfig \
#    && rm -rf /tmp/mpich-build

### Add Cray libraries ###
#WORKDIR /
#RUN printf "/opt/cray/pe/mpt/7.7.0/gni/mpich-gnu-abi/5.1/lib\n" >> /etc/ld.so.conf \
#    && printf "/opt/cray/pmi/default/lib64\n/opt/cray/ugni/default/lib64\n" >> /etc/ld.so.conf \
#    && printf "/opt/cray/udreg/default/lib64\n/opt/cray/xpmem/default/lib64\n" >> /etc/ld.so.conf \
#    && printf "/opt/cray/alps/default/lib64\n/opt/cray/wlm_detect/default/lib64\n" >> /etc/ld.so.conf \
#    && ldconfig

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

#ENV PATH=$PATH:$NETCDF4_DIR/bin
#ENV LD_LIBRARY_PATH=$NETCDF4_DIR/lib:$LD_LIBRARY_PATH
#ENV PKG_CONFIG_PATH=$NETCDF4_DIR/lib/pkgconfig:$PKG_CONFIG_PATH

#netcdf fortran
ENV v=4.4.4
WORKDIR /tmp/netcdf-fortran
RUN wget http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-fortran-${v}.tar.gz \
    && tar -xf netcdf-fortran-${v}.tar.gz \
    && cd netcdf-fortran-${v} \
    && ./configure --prefix=/usr/local \
    && make -j 8 \
    && make install \
    && rm -rf /tmp/netcdf-fortran

#mpi
#ENV v=3.2
#ENV MPICH2_3_2_DIR="/root/Downloads/libraries/mpich-3.2"
#RUN wget http://www.mpich.org/static/downloads/${v}/mpich-${v}.tar.gz \
#    && tar -xzf mpich-${v}.tar.gz \
#    && cd mpich-${v} \
#    && ./configure --prefix=$MPICH2_3_2_DIR \
#    && make \
#    && make install

#ENV PATH=$PATH:$MPICH2_3_2_DIR/bin
ENV LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
ENV PKG_CONFIG_PATH=/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH

#delft3d
# NOTE: we need to replace the '~' with the actual path as it causes
# errors in the delft3d build script
#RUN df -h
ADD dflow_64306 /delft3d
RUN cd /delft3d/src \
  && ./build_ubuntu1604.sh -gnu -64bit

ENV D3D_HOME=/delft3d/src
ENV LD_LIBRARY_PATH=/delft3d/src/lib:$LD_LIBRARY_PATH
ENV PATH=/delft3d/src/bin:$PATH

WORKDIR /delft3d/src/bin

ADD swan.sh /delft3d/src/bin/swan.sh

#RUN cp /root/Downloads/libraries/mpich-3.2/bin/* /delft3d/bin/lnx64/flow2d3d/bin/
#RUN cp -R /root/Downloads/libraries/mpich-3.2/lib/* /delft3d/bin/lnx64/flow2d3d/bin/


