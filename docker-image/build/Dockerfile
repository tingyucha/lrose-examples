## -*- docker-image-name: "lrose-blaze-build" -*-

FROM ubuntu

LABEL version="lrose-blaze-build"
LABEL vendor="Joint NCAR CSU"
LABEL release-date="2018-04-01"

# Add a non-root user (should we make this the default user?)

RUN useradd lrose
WORKDIR /home/lrose

COPY checkout_and_build_auto.py /home/lrose/

# Add libs needed to build lrose and run the lrose components

RUN apt-get update && apt-get install -y  \
    libbz2-dev libx11-dev libpng12-dev libfftw3-dev \
    libjasper-dev qtbase5-dev git \
    libqt5gui5 libqt5core5a qt5-default libx11-6 \
    libfreetype6 gcc g++ gfortran libfl-dev \
    automake make libtool pkg-config libexpat1-dev python
    
WORKDIR /home/lrose
RUN ln -s /usr/lib/x86_64-linux-gnu/qt5/bin/qmake /usr/bin/qmake-qt5 && \
    ./checkout_and_build_auto.py --debug --package lrose-blaze --prefix \
         /usr/local/lrose --clean

# Clean up stuff to reduce the size

RUN apt-get --purge remove -y \
    libbz2-dev libx11-dev libpng12-dev libfftw3-dev \
    libjasper-dev qtbase5-dev git \
    automake make libtool pkg-config libexpat1-dev python \
    language-pack-en libfl-dev
