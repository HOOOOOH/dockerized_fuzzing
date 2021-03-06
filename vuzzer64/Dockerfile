FROM zjuchenyuan/base

RUN apt install -y unzip bmagic python-pip python3-pip libffi-dev &&\
    wget https://software.intel.com/sites/landingpage/pintool/downloads/pin-3.7-97619-g0d0c92f4f-gcc-linux.tar.gz &&\
    tar xf pin-3.7-97619-g0d0c92f4f-gcc-linux.tar.gz &&\
    wget https://github.com/lemire/EWAHBoolArray/archive/0.4.0.zip -O EWAHBoolArray.zip &&\
    unzip EWAHBoolArray.zip &&\
    cp EWAHBoolArray-0.4.0/headers/* /usr/include/ &&\
    pip install BitVector

ENV PIN_HOME=/pin-3.7-97619-g0d0c92f4f-gcc-linux \
    PIN_ROOT=/pin-3.7-97619-g0d0c92f4f-gcc-linux \
    DFT_HOME=/vuzzer64/libdft64

# patch for using BB-weight-angr.py under python3 and newer angr
RUN pip3 install angr==8.19.7.25 networkx==1.11 angr-utils &&\
    git clone https://github.com/ash09/angr-static-analysis-for-vuzzer64 &&\
    cd /angr-static-analysis-for-vuzzer64 &&\
    2to3 -w BB-weight-angr.py &&\
    sed -i 's/main_bin/main_object/g' BB-weight-angr.py &&\
    sed -i 's/CFGAccurate/CFGEmulated/g' BB-weight-angr.py &&\
    sed -i 's/"w"/"wb"/' BB-weight-angr.py &&\
    sed -i "s/'w'/'wb'/" BB-weight-angr.py &&\
    sed -i "s#'_hash', 'soot_block'#'_hash', 'soot_block', 'weight'#" /usr/local/lib/python3.5/dist-packages/angr/knowledge_plugins/cfg/cfg_node.py

ADD patchfile /

RUN git clone https://github.com/vusec/vuzzer64 &&\
    patch -p0 < patchfile &&\
    cd vuzzer64/fuzzer-code &&\
    make &&\
    cd ../libdft64 &&\
    make

