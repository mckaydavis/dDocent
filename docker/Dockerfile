# Dockerfile for dDocent FreeBayes ddRAD / ezRAD sequencing pipeline
#
# derived from https://github.com/jpuritz/dDocent/blob/master/install_dDocent.FB_requirements
# developed and tested with Docker version 0.9.1, build 3600720

from ubuntu:14.04
maintainer https://github.com/McKayDavis

run echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

run sed -i 's:archive.ubuntu.com/ubuntu/:mirror.ancl.hawaii.edu/linux/ubuntu/:' /etc/apt/sources.list
run apt-get update
run apt-get install -y git
run apt-get install -y gcc
run apt-get install -y make
run apt-get install -y curl
run apt-get install -y g++
run apt-get install -y cmake
run apt-get install -y python
run apt-get install -y zlib1g-dev
run apt-get install -y libncurses-dev
run apt-get install -y libpython-dev
run apt-get install -y wget
run apt-get install -y cmake
run apt-get install -y unzip
run apt-get install -y bc


env DDOCENT_DIR /usr/local/ddocent
env DDOCENT_BUILD_DIR ${DDOCENT_DIR}/build
env DDOCENT_INSTALL_DIR ${DDOCENT_DIR}/install

# note about the run true \ ... && true style:
# This is done to simplfy moving and cut'n'paste of
# the actual intermediate commands between invokations of run
# Every acutal command line starts with a && and ends with a backslash

run true \
    && mkdir -p ${DDOCENT_BUILD_DIR} \
    && true

# STACKS 1.12
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://creskolab.uoregon.edu/stacks/source/stacks-1.12.tar.gz  \
    && tar xvzf stacks-1.12.tar.gz \
    && cd stacks-1.12 \
    && ./configure --prefix=${DDOCENT_INSTALL_DIR}/stacks \
    && make -j4 \
    && make install \
    && true


# FreeBayes latest
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && git clone --recursive git://github.com/ekg/freebayes.git \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR}/freebayes \
    && true

# FreeBayes performs a checkout and build of BamTools
# Modify the Makefile to pass the install path to the CMake config of BamTools
# Also modify the BamTools library generation to not build a Shared Object lib (.so)
# and link the static libbamtools.a instead. This fixes an issue where the installed
# bin/bamtools cannot find the .so
run true \
    && sed -i "s:cmake .. && \$(MAKE):cmake .. -DCMAKE_INSTALL_PREFIX=${DDOCENT_INSTALL_DIR}/bamtools \&\& \$\(MAKE\) install:g" ${DDOCENT_BUILD_DIR}/freebayes/src/Makefile \
    && sed -i "s:LIBRARY :ARCHIVE :g" ${DDOCENT_BUILD_DIR}/freebayes/bamtools/src/api/CMakeLists.txt \
    && sed -i "s:SHARED :STATIC :g" ${DDOCENT_BUILD_DIR}/freebayes/bamtools/src/api/CMakeLists.txt \
    && true

run true \
    && make -j4 -C ${DDOCENT_BUILD_DIR}/freebayes \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/freebayes/bin \
    && cp ${DDOCENT_BUILD_DIR}/freebayes/bin/* ${DDOCENT_INSTALL_DIR}/freebayes/bin \
    && true

run true \
    && cp -r ${DDOCENT_BUILD_DIR}/freebayes/python ${DDOCENT_INSTALL_DIR}/freebayes/ \
    && true

run true \
    && cp -r ${DDOCENT_BUILD_DIR}/freebayes/scripts ${DDOCENT_INSTALL_DIR}/freebayes/ \
    && true

run true \
    && cp -r ${DDOCENT_BUILD_DIR}/freebayes/examples ${DDOCENT_INSTALL_DIR}/freebayes/ \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/vcflib/bin \
    && cp ${DDOCENT_BUILD_DIR}/freebayes/vcflib/bin/* ${DDOCENT_INSTALL_DIR}/vcflib/bin \
    && true

# CutAdapt 1.2.1
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O  http://cutadapt.googlecode.com/files/cutadapt-1.2.1.tar.gz \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && tar xvzf cutadapt-1.2.1.tar.gz \
    && cd cutadapt-1.2.1 \
    && python setup.py build \
    && python setup.py build_ext -i \
    && python setup.py install \
    && true

# FastQC 0.10.1
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v0.10.1.zip \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && unzip fastqc_v0.10.1.zip \
    && cd FastQC \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/fastqc/bin \
    && cp fastqc ${DDOCENT_INSTALL_DIR}/fastqc/bin \
    && true

# Trim Galore 0.3.3
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://www.bioinformatics.babraham.ac.uk/projects/trim_galore/trim_galore_v0.3.3.zip \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/trim_galore/bin \
    && unzip ${DDOCENT_BUILD_DIR}/trim_galore_v0.3.3.zip -d ${DDOCENT_INSTALL_DIR}/trim_galore/bin \
    && true

# BWA head

# BWA checkout
run true \
    && git clone https://github.com/lh3/bwa.git ${DDOCENT_BUILD_DIR}/bwa \
    && true

# BWA build
run true \
    && cd ${DDOCENT_BUILD_DIR}/bwa \
    && make -j4 \
    && true

# BWA install
run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/bwa/bin \
    && cp ${DDOCENT_BUILD_DIR}/bwa/bwa ${DDOCENT_INSTALL_DIR}/bwa/bin \
    && true



# SAMTools

# SAMTools download
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://sourceforge.net/projects/samtools/files/samtools/0.1.19/samtools-0.1.19.tar.bz2 \
    && true

# SAMTools extract and make
run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && tar xvjf samtools-0.1.19.tar.bz2 \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR}/samtools-0.1.19 \
    && make -j4 \
    && make -j4 razip\
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/samtools/bin \
    && find ${DDOCENT_BUILD_DIR}/samtools-0.1.19 -executable -type f | xargs -i cp {} ${DDOCENT_INSTALL_DIR}/samtools/bin \
    && true


run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://sourceforge.net/projects/bio-rainbow/files/rainbow_2.0.3.tar.gz \
    && tar xvzf rainbow_2.0.3.tar.gz \
    && true


run true \
    && cd ${DDOCENT_BUILD_DIR}/rainbow_2.0.3 \
    && make -j4 \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/rainbow/bin \
    && find ${DDOCENT_BUILD_DIR}/rainbow_2.0.3 -executable -type f | xargs -i cp {} ${DDOCENT_INSTALL_DIR}/rainbow/bin \
    && true



run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O http://sourceforge.net/projects/gnuplot/files/gnuplot/4.6.5/gnuplot-4.6.5.tar.gz \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && tar xvzf gnuplot-4.6.5.tar.gz \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR}/gnuplot-4.6.5 \
    && ./configure --prefix=${DDOCENT_INSTALL_DIR}/gnuplot \
    && make -j4 \
    && make install \
    && true

# seqtk download
run true \
    && git clone https://github.com/lh3/seqtk.git ${DDOCENT_BUILD_DIR}/seqtk \
    && true

# seqtk build
run true \
    && cd ${DDOCENT_BUILD_DIR}/seqtk \
    && make -j4 \
    && true

# seqtk install
run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/seqtk/bin \
    && cp ${DDOCENT_BUILD_DIR}/seqtk/seqtk ${DDOCENT_INSTALL_DIR}/seqtk/bin \
    && true


run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O https://cdhit.googlecode.com/files/cd-hit-v4.6.1-2012-08-27.tgz \
    && true


run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && tar xvzf cd-hit-v4.6.1-2012-08-27.tgz \
    && cd cd-hit-v4.6.1-2012-08-27 \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/cd-hit/bin \
    && make -j4 openmp=yes \
    && make PREFIX=${DDOCENT_INSTALL_DIR}/cd-hit/bin install \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && curl -L -O https://github.com/arq5x/bedtools2/releases/download/v2.19.1/bedtools-2.19.1.tar.gz \
    && true

run true \
    && cd ${DDOCENT_BUILD_DIR} \
    && tar xvzf bedtools-2.19.1.tar.gz \
    && cd bedtools2-2.19.1 \
    && make -j4 \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/bedtools \
    && cp -r ${DDOCENT_BUILD_DIR}/bedtools2-2.19.1/bin ${DDOCENT_INSTALL_DIR}/bedtools \
    && true

run true \
    && mkdir -p ${DDOCENT_INSTALL_DIR}/ddocent/bin \
    && cd ${DDOCENT_INSTALL_DIR}/ddocent/bin \
    && curl -L -O http://seq-filter.googlecode.com/files/Seq_filter.pl \
    && curl -L -O http://nash-bioinformatics-codelets.googlecode.com/files/cutseq_fasta.pl \
    && curl -L -O https://github.com/McKayDavis/dDocent/raw/master/mergefq.pl \
    && curl -L -O https://github.com/McKayDavis/dDocent/raw/master/dDocent.FB \
    && chmod +x * \
    && true

# Modify the container PATH to find all the dDocent scripts
env PATH ${DDOCENT_INSTALL_DIR}/bamtools/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/bedtools/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/bwa/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/cd-hit/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/ddocent/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/fastqc/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/freebayes/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/gnuplot/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/rainbow/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/samtools/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/seqtk/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/stacks/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/trim_galore/bin:${PATH}
env PATH ${DDOCENT_INSTALL_DIR}/vcflib/bin:${PATH}
