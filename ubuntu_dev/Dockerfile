FROM ubuntu:20.04
ARG DEBIAN_FRONTEND=noninteractive
ARG HTTPS_PROXY
ENV TZ=Asia/Shanghai

#stage0: install compile toolchain
COPY ./sources.list /etc/apt/sources.list
ENV https_proxy=${HTTPS_PROXY}
RUN echo "http_proxy is ${https_proxy}"
RUN apt-get update && DEBIAN_FRONTEND="noninteractive" TZ="Asia/Shanghai" apt-get install -y tzdata  && \ 
    apt-get install -y wget git curl zip unzip tar && \ 
    apt-get install -y build-essential &&  \ 
    apt-get install -y ninja-build valgrind gdb && \ 
    apt-get install -y libipc-run-perl pkg-config && \ 
    apt-get install -y make python3 python3-pip && \
    apt-get install -y autoconf libtool autopoint gettext gawk && \
    apt-get install -y arping iperf netperf && \ 
    apt-get install -y zlib1g-dev libelf-dev libfl-dev libedit-dev  && \ 
    apt-get install -y libllvm13 llvm-13-dev libclang-13-dev  && \ 
    apt-get install -y luajit luajit-5.1-dev && \ 
    apt-get install -y binutils-dev libelf-dev libcap-dev && \ 
    apt-get install -y flex bison && \
    apt-get install -y google-perftools && \
    apt-get install -y linux-source trace-cmd && \
    rm -rf /var/lib/apt/lists/*

ENV CMAKE_VERSION=3.22.2
RUN curl -L -o cmake-linux.sh  https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-Linux-x86_64.sh && \
    sh cmake-linux.sh -- --skip-license --prefix=/usr/local && \
    rm cmake-linux.sh

# stage1 dev base terminal
RUN git clone https://github.com/iovisor/bcc.git /opt/bcc && \ 
    cd /opt/bcc && git checkout v0.24.0 && mkdir -p build &&  \ 
    cd build && cmake .. -DCMAKE_PREFIX_PATH=/usr/lib/llvm-13 && \ 
    make -j20 && make install && cmake -DPYTHON_CMD=python3 .. && \ 
    cd src/python && make && make install


RUN cd /usr/src/linux-source-5.4.0 && \ 
    tar xf linux-source-5.4.0.tar.bz2 && \ 
    cd linux-source-5.4.0/tools/perf && \ 
    make -j10 && make install && \ 
    cp ./perf /usr/bin/perf && \ 
    cd .. && \
    cd ./bpf/bpftool &&  \ 
    make && make install

RUN git clone https://github.com/brendangregg/FlameGraph.git /opt/FlameGraph &&  \ 
    git clone https://github.com/brendangregg/perf-tools.git /opt/perf-tools

# stage2 :dev env define a basic nvim dev config
ENV NVIM_VERSION=v0.7.2
RUN wget --no-check-certificate https://github.com/neovim/neovim/releases/download/${NVIM_VERSION}/nvim-linux64.tar.gz -P /opt/nvim-linux64 && \
    cd /opt/nvim-linux64/ && \
    tar xf nvim-linux64.tar.gz  && \
    echo "alias vim=/opt/nvim-linux64/nvim-linux64/bin/nvim" >> /root/.bashrc && \
    python3 -m pip install --user --upgrade pynvim && \
    sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.2/zsh-in-docker.sh)" -- -t robbyrussell && \ 
    curl -s https://deb.nodesource.com/gpgkey/nodesource.gpg.key | gpg --dearmor | tee /usr/share/keyrings/nodesource.gpg >/dev/null && \ 
    curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | tee /usr/share/keyrings/yarnkey.gpg >/dev/null && \
    echo 'deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_16.x focal main' > /etc/apt/sources.list.d/nodesource.list && \ 
    echo 'deb-src [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_16.x focal main' >> /etc/apt/sources.list.d/nodesource.list && \ 
    echo 'deb [signed-by=/usr/share/keyrings/yarnkey.gpg] https://dl.yarnpkg.com/debian stable main' >> /etc/apt/sources.list.d/yarn.list && \
    apt-get update && \ 
    apt-get install -y nodejs yarn golang tmux tldr clangd && \ 
    echo "alias vim=/opt/nvim-linux64/nvim-linux64/bin/nvim" >> /root/.zshrc && \
    tldr tar >/dev/null && \
    curl -fLo /root/.config/nvim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim && \ 
    curl -fLo /root/.config/nvim/init.vim --create-dirs https://raw.githubusercontent.com/hudengjunai/dotfiles/master/init.vim && \ 
    curl -fLo /root/.config/nvim/coco_config.vim --create-dirs https://raw.githubusercontent.com/hudengjunai/dotfiles/master/coco_config.vim && \
    curl -fLo /root/.config/nvim/latex.vim --create-dirs https://raw.githubusercontent.com/hudengjunai/dotfiles/master/latex.vim && \ 
    rm -rf /var/lib/apt/lists/*


RUN ["/bin/bash", "-c", "/opt/nvim-linux64/nvim-linux64/bin/nvim +PlugInstall +qall",">/dev/null"]
RUN cd /root/.config/nvim/plugged/coc.nvim && yarn install >/dev/null

#stage1: install java maven
RUN wget --no-check-certificate https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz -P /tmp/openjdk  && \
    tar xf /tmp/openjdk/openjdk-11+28_linux-x64_bin.tar.gz -C /opt && \ 
    rm /tmp/openjdk/*

ENV JAVA_HOME=/opt/jdk-11
ENV PATH="${JAVA_HOME}/bin:/opt/maven/bin:${PATH}"

#ENV https_proxy=http://dl-proxy.service.163.org:8123
ENV MVN_VERSION=3.8.6
RUN wget --no-check-certificate https://dlcdn.apache.org/maven/maven-3/${MVN_VERSION}/binaries/apache-maven-${MVN_VERSION}-bin.tar.gz -P /tmp/mvn && \
    tar xf /tmp/mvn/apache-maven-${MVN_VERSION}-bin.tar.gz -C /opt && \
    ln -s /opt/apache-maven-${MVN_VERSION} /opt/maven && rm -rf /tmp/mvn/*
    #curl -L -o cmake-linux.sh  https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-Linux-x86_64.sh && \
    #sh cmake-linux.sh -- --skip-license --prefix=/usr/local



#stage2 install g++10.2 gdb-10.2 cmake 3.22.2 ninja valgrind-3.16+ systemtap

# stage3: install vcpkg and basic lib
#RUN rm -rf /var/lib/apt
RUN git clone --depth 1  --branch 2022.04.12 https://github.com/microsoft/vcpkg /opt/vcpkg && cd /opt/vcpkg  && \
    bash ./bootstrap-vcpkg.sh && ./vcpkg update

RUN /opt/vcpkg/vcpkg install protobuf abseil openssl "c-ares" re2 && \
    /opt/vcpkg/vcpkg install gtest gflags glog grpc abseil benchmark && \
    /opt/vcpkg/vcpkg install boost-core boost-asio boost-filesystem && \
    /opt/vcpkg/vcpkg install tbb antlr4 cppitertools && \ 
    /opt/vcpkg/vcpkg install ParallelSTL yaml-cpp xxhash cityhash && \
    /opt/vcpkg/vcpkg install rapidjson jsoncons utfcpp cpp-base64 && \
    /opt/vcpkg/vcpkg install librdkafka nlohmann-json magic-enum rapidxml fmt libuuid && \
    /opt/vcpkg/vcpkg install gperftools && \ 
    /opt/vcpkg/vcpkg install continuable libcds roaring libuv && \
    /opt/vcpkg/vcpkg install "prometheus-cpp[push]" --recurse hiredis boost-stacktrace breakpad && \
    /opt/vcpkg/vcpkg install asio libsodium zstd elfutils && \
    /opt/vcpkg/vcpkg install boost-program-options boost-crc double-conversion libevent ftxui && \ 
    /opt/vcpkg/vcpkg install brpc braft --recurse && \ 
    /opt/vcpkg/vcpkg install spdlog boost-logic boost-outcome && \
    /opt/vcpkg/vcpkg install arrow parquet nanobench  boost-bimap fruit range-v3 murmurhash boost-thread && \
    /opt/vcpkg/vcpkg install rttr kangaru bext-di && \
    rm -rf /opt/vcpkg/packages &&  \ 
    rm -rf /opt/vcpkg/downloads &&  \ 
    rm -rf /opt/vcpkg/buildtrees 


RUN git clone  https://github.com/hudengjunai/vcpkg_libs.git /tmp/vcpkg_libs && \ 
    rm -rf /opt/vcpkg/ports/mpmcqueue && \ 
    rm -rf /opt/vcpkg/ports/snappy && \
    rm -rf /opt/vcpkg/ports/folly && \
    rm -rf /opt/vcpkg/ports/bshoshany-thread-pool  && \
    cp -r /tmp/vcpkg_libs/cmake/* /opt/vcpkg/ports && \ 
    /opt/vcpkg/vcpkg install "redis-plus-plus[cxx17]" curlpp poco modern-cpp-kafka datasketches && \ 
    /opt/vcpkg/vcpkg install rtdsync snappy bshoshany-thread-pool && \
    /opt/vcpkg/vcpkg install mpmcqueue folly && \
    rm -rf /opt/vcpkg/packages &&  \ 
    rm -rf /opt/vcpkg/downloads &&  \ 
    rm -rf /opt/vcpkg/buildtrees 
RUN apt -y remove golang && apt -y autoremove && \ 
    cd /opt && wget --no-check-certificate https://go.dev/dl/go1.19.linux-amd64.tar.gz && \ 
    tar -xf go1.19.linux-amd64.tar.gz -C /usr/local &&  \ 
    wget --no-check-certificate https://github.com/bojand/ghz/releases/download/v0.109.0/ghz-linux-x86_64.tar.gz && \ 
    tar -xf ghz-linux-x86_64.tar.gz  -C /usr/local && \
    rm -rf *.tar.gz

<<<<<<< HEAD

=======
RUN apt-get update && apt install -y  lsb-core  && \ 
    git clone --depth 1  --branch release/1.83 https://github.com/google/orbit.git /opt/orbit && cd /opt/orbit && \
    apt-get install sudo && \
    apt-get install -y net-tools && \
    apt-get install -y sysstats && \
    rm -rf /var/lib/apt/lists/*
    #&& \
    #echo "#!/bin/bash\n\$@" > /usr/bin/sudo && \
    #chmod +x /usr/bin/sudo
>>>>>>> 467007a (update some lib)
ENV PATH="/usr/local/go/bin:/root/.local/bin:$PATH"
RUN ln -s /usr/local/ghz /usr/bin/ghz
#RUN cd /opt/orbit && \ 
    #bash ./bootstrap-orbit.sh

# install hotspot,heaptrack,uftrace to profile and monitor the application
ENV https_proxy=""

