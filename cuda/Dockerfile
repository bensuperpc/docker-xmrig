ARG BUIDER_IMAGE=nvidia/cuda:11.3.1-devel-ubuntu20.04
ARG RUNTIME_IMAGE=nvidia/cuda:11.3.1-runtime-ubuntu20.04
FROM $BUIDER_IMAGE as cuda-builder


LABEL author="Bensuperpc <bensuperpc@gmail.com>"
LABEL mantainer="Bensuperpc <bensuperpc@gmail.com>"

ARG DEBIAN_FRONTEND=noninteractive

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

FROM $BUIDER_IMAGE as xmrig-builder

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
   && apt-get install -y  git build-essential cmake automake libtool autoconf wget ninja-build jq \
	&& apt-get clean autoclean \
	&& apt-get autoremove --yes \
	&& rm -rf /var/lib/{apt,dpkg,cache,log}/

RUN git clone --recursive https://github.com/xmrig/xmrig.git /tmp/xmrig \
   && cd /tmp/xmrig \
   && contents="$(jq '.cuda.enabled = true' src/config.json)" \
   && echo "${contents}" > src/config.json \
   && mkdir -p build \
   && cd scripts && ./build_deps.sh && cd ../build \
   && cmake .. -DXMRIG_DEPS=scripts/deps -DBUILD_STATIC=OFF -DWITH_CUDA=ON -DWITH_OPENCL=OFF -GNinja \
   && ninja

FROM $RUNTIME_IMAGE as runtime

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
   && apt-get install -y libuv1-dev libssl1.1 libhwloc15 \
	&& apt-get clean autoclean \
	&& apt-get autoremove --yes \
	&& rm -rf /var/lib/{apt,dpkg,cache,log}/

COPY --from=cuda-builder /tmp/xmrig-cuda/build/libxmrig-cuda.so /usr/local/bin/libxmrig-cuda.so
COPY --from=xmrig-builder /tmp/xmrig/build/xmrig /usr/local/bin/xmrig
COPY --from=xmrig-builder /tmp/xmrig/src/config.json /usr/local/bin/config.json

ENV PATH="/usr/local/bin:${PATH}"

RUN xmrig --version

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