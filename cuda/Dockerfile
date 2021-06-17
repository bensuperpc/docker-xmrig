ARG BUIDER_IMAGE=nvidia/cuda:11.3.1-devel-ubuntu20.04
FROM $BUIDER_IMAGE as builder

ARG DEBIAN_FRONTEND=noninteractive

LABEL author="Bensuperpc <bensuperpc@gmail.com>"
LABEL mantainer="Bensuperpc <bensuperpc@gmail.com>"

ARG VERSION="1.0.0"
ENV VERSION=$VERSION

RUN apt-get update \
   && apt-get install -y git build-essential cmake libuv1-dev libssl-dev libhwloc-dev wget ninja-build \
	&& apt-get clean autoclean \
	&& apt-get autoremove --yes \
	&& rm -rf /var/lib/{apt,dpkg,cache,log}/

RUN git clone --recursive https://github.com/xmrig/xmrig-cuda.git /tmp/xmrig-cuda \
   && cd /tmp/xmrig-cuda \
   && mkdir -p build \
   && cd build \
   && cmake .. -DCUDA_LIB=/usr/local/cuda/lib64/stubs/libcuda.so -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda -GNinja \
   && ninja

RUN mv /tmp/xmrig-cuda/build/xmrig-cuda /usr/local/bin/xmrig-cuda

ARG RUNTIME_IMAGE=cuda:11.3.1-runtime-ubuntu20.04
FROM $RUNTIME_IMAGE as runtime

RUN apt-get update \
   && apt-get install -y libuv1 libssl libhwloc \
	&& apt-get clean autoclean \
	&& apt-get autoremove --yes \
	&& rm -rf /var/lib/{apt,dpkg,cache,log}/

COPY --from=builder /tmp/xmrig-cuda/build/xmrig-cuda /usr/local/bin/xmrig-cuda

ENV PATH="/usr/local/bin:${PATH}"

RUN xmrig-cuda --version

LABEL org.label-schema.schema-version="1.0" \
   org.label-schema.build-date=$BUILD_DATE \
   org.label-schema.name="bensuperpc/xmrig" \
   org.label-schema.description="xmrig in docker" \
   org.label-schema.version=$VERSION \
   org.label-schema.vendor="Bensuperpc" \
   org.label-schema.url="http://bensuperpc.com/" \
   org.label-schema.vcs-url="https://github.com/Bensuperpc/docker-xmrig" \
   org.label-schema.vcs-ref=$VCS_REF \
   org.label-schema.docker.cmd="docker build -t bensuperpc/xmrig -f Dockerfile ."

CMD ["xmrig-cuda", "--version"]