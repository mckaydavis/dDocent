FROM ubuntu:14.04
MAINTAINER https://github.com/McKayDavis
RUN mkdir -p /dDocent
RUN mkdir /dDocent/bin
RUN mkdir /dDocent/build
RUN cd /dDocent/build
run apt-get install -y git gcc
run apt-get install -y make
run apt-get install -y curl
run apt-get install -y g++
run apt-get install -y cmake
run apt-get install -y python
run apt-get install -y zlib1g-dev
run apt-get install -y libncurses-dev
run apt-get install -y libpython-dev
run apt-get install -y wget

ADD https://github.com/jpuritz/dDocent/raw/master/install_dDocent.FB_requirements /dDocent/
RUN bash /dDocent/install_dDocent.FB_requirements /dDocent/bin
