#//////////////////////////////////////////////////////////////
#//   ____                                                   //
#//  | __ )  ___ _ __  ___ _   _ _ __   ___ _ __ _ __   ___  //
#//  |  _ \ / _ \ '_ \/ __| | | | '_ \ / _ \ '__| '_ \ / __| //
#//  | |_) |  __/ | | \__ \ |_| | |_) |  __/ |  | |_) | (__  //
#//  |____/ \___|_| |_|___/\__,_| .__/ \___|_|  | .__/ \___| //
#//                             |_|             |_|          //
#//////////////////////////////////////////////////////////////
#//                                                          //
#//  Script, 2021                                            //
#//  Created: 11, June, 2021                                 //
#//  Modified: 11, June, 2021                                //
#//  file: -                                                 //
#//  -                                                       //
#//  Source: https://xmrig.com/docs/miner/build/ubuntu                                                //
#//          https://www.docker.com/blog/getting-started-with-docker-for-arm-on-linux/
#//          https://schinckel.net/2021/02/12/docker-%2B-makefile/
#//          https://www.padok.fr/en/blog/multi-architectures-docker-iot
#//  OS: ALL                                                 //
#//  CPU: ALL                                                //
#//                                                          //
#//////////////////////////////////////////////////////////////
BUIDER_IMAGE := nvidia/cuda:11.3.1-devel-ubuntu20.04
RUNTIME_IMAGE := nvidia/cuda:11.3.1-runtime-ubuntu20.04
IMAGE_SUBTAG := cuda
IMAGE_NAME := bensuperpc/xmrig
DOCKERFILE := Dockerfile

DOCKER := docker

TAG := $(shell date '+%Y%m%d')-$(shell git rev-parse --short HEAD)
DATE_FULL := $(shell date -u "+%Y-%m-%dT%H:%M:%SZ")
UUID := $(shell cat /proc/sys/kernel/random/uuid)
VERSION := 1.0.0

# linux/386 linux/arm64 linux/ppc64le linux/s390x linux/arm/v7 linux/arm/v6
ARCH_LIST := linux/amd64
comma:= ,
COM_ARCH_LIST:= $(subst $() $(),$(comma),$(ARCH_LIST))

#-t $(IMAGE_NAME):latest
$(ARCH_LIST): $(DOCKERFILE)
	$(DOCKER) buildx build . -f $(DOCKERFILE) -t $(IMAGE_NAME):$(IMAGE_SUBTAG)-$(TAG) \
	-t $(IMAGE_NAME):$(IMAGE_SUBTAG) --build-arg BUILD_DATE=$(DATE_FULL) -t $(IMAGE_NAME):$(IMAGE_SUBTAG)-latest \
	--build-arg DOCKER_IMAGE=$(BUIDER_IMAGE) --platform $@ --build-arg VERSION=$(VERSION) \
	--build-arg RUNTIME_IMAGE=$(RUNTIME_IMAGE) --load

	
all: $(DOCKERFILE)
	$(DOCKER) buildx build . -f $(DOCKERFILE) -t $(IMAGE_NAME):$(IMAGE_SUBTAG)-$(TAG) -t $(IMAGE_NAME):latest \
	-t $(IMAGE_NAME):$(IMAGE_SUBTAG) --build-arg BUILD_DATE=$(DATE_FULL) -t $(IMAGE_NAME):$(IMAGE_SUBTAG)-latest \
	--build-arg DOCKER_IMAGE=$(BUIDER_IMAGE) --platform $(COM_ARCH_LIST) --build-arg VERSION=$(VERSION) \
	--build-arg RUNTIME_IMAGE=$(RUNTIME_IMAGE) --push

push: all

# https://github.com/linuxkit/linuxkit/tree/master/pkg/binfmt
qemu:
	export DOCKER_CLI_EXPERIMENTAL=enabled
	$(DOCKER) run --rm --privileged multiarch/qemu-user-static --reset -p yes
	$(DOCKER) buildx create --name qemu_builder --driver docker-container --use
	$(DOCKER) buildx inspect --bootstrap

clean:
	$(DOCKER) images --filter='reference=$(IMAGE_NAME)' --format='{{.Repository}}:{{.Tag}}' | xargs -r $(DOCKER) rmi -f

.PHONY: build push clean qemu $(ARCH_LIST)
