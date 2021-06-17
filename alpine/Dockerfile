ARG DOCKER_IMAGE=alpine:3.13
FROM $DOCKER_IMAGE as builder

LABEL author="Bensuperpc <bensuperpc@gmail.com>"
LABEL mantainer="Bensuperpc <bensuperpc@gmail.com>"

ARG VERSION="1.0.0"
ENV VERSION=$VERSION

RUN apk add --no-cache \
      git make cmake libstdc++ gcc g++ automake libtool autoconf linux-headers ninja
RUN git clone --recursive https://github.com/xmrig/xmrig.git /tmp/xmrig \
   && cd /tmp/xmrig \
   && mkdir -p build \
   && cd scripts && ./build_deps.sh && cd ../build \
   && cmake .. -DXMRIG_DEPS=scripts/deps -DBUILD_STATIC=ON -DWITH_CUDA=OFF -DWITH_OPENCL=OFF -GNinja \
   && ninja

ARG DOCKER_IMAGE=alpine:3.13
FROM $DOCKER_IMAGE as runtime

COPY --from=builder /tmp/xmrig/build/xmrig /usr/local/bin/xmrig

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

CMD ["xmrig", "--version"]