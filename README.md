# docker-xmrig
xmrig in docker

[![docker-xmrig](https://github.com/bensuperpc/docker-xmrig/actions/workflows/main.yml/badge.svg)](https://github.com/bensuperpc/docker-xmrig/actions/workflows/main.yml)

[![Twitter](https://img.shields.io/twitter/follow/Bensuperpc?style=social)](https://img.shields.io/twitter/follow/Bensuperpc?style=social) [![Youtube](https://img.shields.io/youtube/channel/subscribers/UCJsQFFL7QW4LSX9eskq-9Yg?style=social)](https://img.shields.io/youtube/channel/subscribers/UCJsQFFL7QW4LSX9eskq-9Yg?style=social) 

## Usage
### CPU
```sh
docker run --rm -it bensuperpc/xmrig:latest xmrig --help
```
### GPU
```sh
docker run --rm -it --gpus all bensuperpc/xmrig:cuda-latest xmrig-cuda --help
```

See for more info: https://xmrig.com/docs/miner
