# docker-nvidia-cuda-ffmpeg
A docker container, with ffmpeg that supports scale_cuda among other things

## Options

To see the options for specific filters use:

### scale_cuda

```bash
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h filter=scale_cuda
```

### overlay_cuda

```bash
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h filter=overlay_cuda
```

### hevc_nvenc

docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h encoder=hevc_nvenc

### hevc_nvenc

docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h encoder=h264_nvenc

## Extras

### Mosaic

Generate a 4, 6 or 9 panel mosaic / overlay.

You can use the mosaic command to generate the ffmpeg command you need, or just let it generate the mosaic and stream it for you.

Just pass the environment variables (details below) and use a command of `mosaic`

#### Required environment variables

| Variable                 | Use                                  | Default                                | Notes                                                                                                                                |
|--------------------------|--------------------------------------|----------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| INPUT0                   | The background image.                | / etc / mosaic / 1920x1080-black.png   | The default is a black png that ships with the container                                                                             |
| INPUT1                   | The first panel URL                  | /etc/mosaic/1920x1080.png              | Supply anything ffmpeg can process                                                                                                   |
| INPUT2                   | The second panel URL                 | "                                      | "                                                                                                                                    |
| INPUT3                   | The third panel URL                  | "                                      | "                                                                                                                                    |
| INPUT4                   | The forth panel URL                  | "                                      | "                                                                                                                                    |
| INPUT5                   | The fifth panel URL                  | "                                      | Only supply these if you are using a 6 panel mosaic                                                                                  |
| INPUT6                   | The sixth panel URL                  | "                                      | "                                                                                                                                    |
| INPUT7                   | The seventh panel URL                | "                                      | Only supply these if you are using a 9 panel mosaic                                                                                  |
| INPUT8                   | The eight panel URL                  | "                                      | "                                                                                                                                    |
| INPUT9                   | The night panel URL                  | "                                      | "                                                                                                                                    |
| CONTAINER                | The output container                 | mpegts                                 | passed to -f ie `-f mpegts` or `-f flv`                                                                                              |
| OUTPUT                   | The output destination               | udp :// 224.0.51.1 : 1234?pkt_size=188 | Where the data should go                                                                                                             |
| BITRATE                  | The output bitrate                   | 8M                                     |                                                                                                                                      |
| ENCODER                  | The encoder to use                   | hevc                                   | h264 or hevc                                                                                                                         |
| RESOLUTION               | Select from a list of defaults       | FHD                                    | Select nHD,qHD,HD,HD+,FHD,DCI 2K,QHD,QHD+,4K UHD to auto set width and height                                                        |
| WIDTH                    | The output mosaic width              | 1920                                   |                                                                                                                                      |
| HEIGHT                   | The output mosaic height             | 1080                                   |                                                                                                                                      |
| FFMPEG_THREAD_QUEUE_SIZE | Thread queue size                    | 512                                    | Tweak this only if you need                                                                                                          |
| FFMPEG_CUDA_FORMAT       | The format inside cuda processing    | nv12                                   | Tweak this only if you need                                                                                                          |
| GENCMD                   | Generate the ffmpeg command if not 0 | 0                                      | Set this to `1` to generate an example command with your settings so that you can tweak it as you desire and run the mosaic yourself |

#### Example Docker Compose

```yaml
---
version: '3.8'

services:
  ffmpeg:
    image: ghcr.io/aperim/nvidia-cuda-ffmpeg:latest
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
    restart: unless-stopped              
    environment:
      PUID: 1000
      PGID: 1000
      TZ: Australia/Sydney
      NVIDIA_VISIBLE_DEVICES: all
      NVIDIA_DRIVER_CAPABILITIES: all
      NVIDIA_REQUIRE_CUDA: cuda>=11.4
      INPUT1: http://demo.unified-streaming.com/video/tears-of-steel/tears-of-steel.ism/.m3u8
      INPUT2: https://multiplatform-f.akamaihd.net/i/multi/will/bunny/big_buck_bunny_,640x360_400,640x360_700,640x360_1000,950x540_1500,.f4v.csmil/master.m3u8
      INPUT3: https://multiplatform-f.akamaihd.net/i/multi/april11/sintel/sintel-hd_,512x288_450_b,640x360_700_b,768x432_1000_b,1024x576_1400_m,.mp4.csmil/master.m3u8
      INPUT4: https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8
    volumes:
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"
      - "/home/operations/mosaic:/mosaic:ro"
    command: mosaic

```

#### Example To Generate Your Own Command

```bash
docker run -it --rm \
    -e GENCMD=1 \
    -e INPUT1=http://demo.unified-streaming.com/video/tears-of-steel/tears-of-steel.ism/.m3u8 \
    -e INPUT2=https://multiplatform-f.akamaihd.net/i/multi/will/bunny/big_buck_bunny_,640x360_400,640x360_700,640x360_1000,950x540_1500,.f4v.csmil/master.m3u8 \
    -e INPUT3=https://multiplatform-f.akamaihd.net/i/multi/april11/sintel/sintel-hd_,512x288_450_b,640x360_700_b,768x432_1000_b,1024x576_1400_m,.mp4.csmil/master.m3u8 \
    -e INPUT4=https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8 \
    ghcr.io/aperim/nvidia-cuda-ffmpeg:latest mosaic
```