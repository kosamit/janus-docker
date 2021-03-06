# Base Image Debian
FROM debian

# OS Update
RUN apt-get update

# Install Required libraries
RUN apt-get install -y --fix-missing libmicrohttpd-dev libjansson-dev libssl-dev \
  libsofia-sip-ua-dev libglib2.0-dev libopus-dev libogg-dev \
  libcurl4-openssl-dev liblua5.3-dev libconfig-dev \
  pkg-config gengetopt libtool automake gtk-doc-tools doxygen \
  graphviz libnanomsg-dev cmake

# Install Required libraries for videoroom recording
RUN apt-get install -y --fix-missing \
  libglib2.0-dev libjansson-dev libavutil-dev libavcodec-dev libavformat-dev vorbis-tools zlib1g-dev bc ffmpeg

# Install the command used for installation
RUN apt-get install -y wget && \
 apt-get install vim -y && \
 apt-get install git -y && \
 apt-get install -y curl

# Install libnice
RUN PATH="/usr/local/bin:$PATH"
RUN yes | apt-get install python3-pip
RUN yes | pip3 install meson
RUN yes | pip3 install ninja
RUN cd /root && \
  git clone https://gitlab.freedesktop.org/libnice/libnice && \
  cd ./libnice && \
  meson --prefix=/usr build && ninja -C build && ninja -C build install

# Install libsrtp
RUN cd /root && \
  wget https://github.com/cisco/libsrtp/archive/v2.2.0.tar.gz && \
  tar xfv v2.2.0.tar.gz && \
  cd libsrtp-2.2.0 && \
  ./configure --prefix=/usr --enable-openssl && \
  make shared_library && make install

# Install usrsctp
RUN cd /root && \
  git clone https://github.com/sctplab/usrsctp && \
  cd ./usrsctp && \
  ./bootstrap && \
  ./configure --prefix=/usr --disable-programs --disable-inet --disable-inet6 && \
   make && make install

# install libwebsockets
RUN cd /root && \
  git clone https://libwebsockets.org/repo/libwebsockets && \
  cd ./libwebsockets && \
  mkdir build && \
  cd ./build && \
  cmake -DLWS_MAX_SMP=1 -DLWS_WITHOUT_EXTENSIONS=0 \
  -DCMAKE_INSTALL_PREFIX:PATH=/usr -DCMAKE_C_FLAGS="-fpic" .. && \
  make && make install

# Install the Janus server itself
RUN cd /root && \
  git clone https://github.com/meetecho/janus-gateway.git && \
  cd janus-gateway && \
  sh autogen.sh && \
  ./configure --prefix=/opt/janus --enable-post-processing && \
  make && \
  make install && \
  make configs

EXPOSE 8088 8188

USER root

CMD ["/opt/janus/bin/janus"]
