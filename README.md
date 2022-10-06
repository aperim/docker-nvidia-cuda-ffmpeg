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

```bash
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h encoder=hevc_nvenc
```

### h264_nvenc

```bash
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest -h encoder=h264_nvenc
```

## Extras

[**Mosaic**](#Mosaic)
Generate a video mosaic using overlays. Easily create 4, 6 and 9 input mosaics.

[**Camera**](#Camera)
Shortcut for transcoding rtsp camera streams.

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
| OUTPUT                   | The output destination               | udp://224.0.51.1:1234?pkt_size=188     | Where the data should go                                                                                                             |
| BITRATE                  | The output bitrate                   | 8M                                     |                                                                                                                                      |
| ENCODER                  | The encoder to use                   | hevc                                   | h264 or hevc                                                                                                                         |
| ENCODE_PRESET            | The encoder preset                   | veryfast                               |                                                                                                                                      |
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
      INPUT1: https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8
      INPUT2: https://d3rlna7iyyu8wu.cloudfront.net/skip_armstrong/skip_armstrong_multichannel_subs.m3u8
      INPUT3: http://amssamples.streaming.mediaservices.windows.net/634cd01c-6822-4630-8444-8dd6279f94c6/CaminandesLlamaDrama4K.ism/manifest(format=m3u8-aapl)
      INPUT4: https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8
    volumes:
      - "/etc/timezone:/etc/timezone:ro"
      - "/etc/localtime:/etc/localtime:ro"
    command: mosaic

```

#### Example To Generate Your Own Command

Running the command below:

```bash
docker run -it --rm \
    -e GENCMD=1 \
    -e INPUT1=https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8 \
    -e INPUT2=https://d3rlna7iyyu8wu.cloudfront.net/skip_armstrong/skip_armstrong_multichannel_subs.m3u8 \
    -e INPUT3=http://amssamples.streaming.mediaservices.windows.net/634cd01c-6822-4630-8444-8dd6279f94c6/CaminandesLlamaDrama4K.ism/manifest\(format=m3u8-aapl\) \
    -e INPUT4=https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8 \
    ghcr.io/aperim/nvidia-cuda-ffmpeg:latest mosaic
```

Would generate this example output

```text
🖥️ mosaic command
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all \
        -e INPUT0="/etc/mosaic/1920x1080-black.png" \
        -e INPUT1="https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8" \
        -e INPUT2="https://d3rlna7iyyu8wu.cloudfront.net/skip_armstrong/skip_armstrong_multichannel_subs.m3u8" \
        -e INPUT3="http://amssamples.streaming.mediaservices.windows.net/634cd01c-6822-4630-8444-8dd6279f94c6/CaminandesLlamaDrama4K.ism/manifest(format=m3u8-aapl)" \
        -e INPUT4="https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8" \
        -e CONTAINER="mpegts" \
        -e OUTPUT="udp://224.0.51.1:1234?pkt_size=188" \
        -e BITRATE="8M" \
        -e ENCODER="hevc" \
        -e WIDTH="1920" \
        -e HEIGHT="1080" \
        -e FFMPEG_THREAD_QUEUE_SIZE="512" \
        -e FFMPEG_CUDA_FORMAT="nv12" \
        ghcr.io/aperim/nvidia-cuda-ffmpeg:latest mosaic

⌨️ ffmpeg command
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest \
        -nostats -y -hide_banner -loglevel warning -err_detect ignore_err \
        -thread_queue_size 512 -hwaccel cuda -hwaccel_output_format nv12 -i /etc/mosaic/1920x1080-black.png \
        -reconnect_on_network_error 1 -reconnect_on_http_error 1 -reconnect_streamed 1 -reconnect_delay_max 2000-thread_queue_size 512 -hwaccel cuda -hwaccel_output_format nv12 -i https://cph-p2p-msl.akamaized.net/hls/live/2000341/test/master.m3u8 \
        -reconnect_on_network_error 1 -reconnect_on_http_error 1 -reconnect_streamed 1 -reconnect_delay_max 2000-thread_queue_size 512 -hwaccel cuda -hwaccel_output_format nv12 -i https://d3rlna7iyyu8wu.cloudfront.net/skip_armstrong/skip_armstrong_multichannel_subs.m3u8 \
        -reconnect_on_network_error 1 -reconnect_on_http_error 1 -reconnect_streamed 1 -reconnect_delay_max 2000-thread_queue_size 512 -hwaccel cuda -hwaccel_output_format nv12 -i http://amssamples.streaming.mediaservices.windows.net/634cd01c-6822-4630-8444-8dd6279f94c6/CaminandesLlamaDrama4K.ism/manifest(format=m3u8-aapl) \
        -reconnect_on_network_error 1 -reconnect_on_http_error 1 -reconnect_streamed 1 -reconnect_delay_max 2000-thread_queue_size 512 -hwaccel cuda -hwaccel_output_format nv12 -i https://devimages.apple.com.edgekey.net/iphone/samples/bipbop/bipbopall.m3u8 \
        -filter_complex " \
         \
        [0:v]format=nv12,hwupload_cuda,scale_cuda=-2:w=1920:h=1080:format=nv12[base]; \
        [1:v]format=nv12,hwupload_cuda,scale_cuda=-2:w=960:h=540:format=nv12,fps=24,setpts=PTS-STARTPTS[panel1]; \
        [2:v]format=nv12,hwupload_cuda,scale_cuda=-2:w=960:h=540:format=nv12,fps=24,setpts=PTS-STARTPTS[panel2]; \
        [3:v]format=nv12,hwupload_cuda,scale_cuda=-2:w=960:h=540:format=nv12,fps=24,setpts=PTS-STARTPTS[panel3]; \
        [4:v]format=nv12,hwupload_cuda,scale_cuda=-2:w=960:h=540:format=nv12,fps=24,setpts=PTS-STARTPTS[panel4]; \
        [base][panel1]overlay_cuda=shortest=0:x=0:y=0[layer1]; \
        [layer1][panel2]overlay_cuda=shortest=0:x=960:y=0[layer2]; \
        [layer2][panel3]overlay_cuda=shortest=0:x=0:y=540[layer3]; \
        [layer3][panel4]overlay_cuda=shortest=0:x=960:y=540[final] \
         \
        " \
        -map "[final]" -c:v hevc_nvenc -preset fast -tune ull -zerolatency 1 -b:v 8M \
        -map 1:a:1\? -map 2:a:2\? -map 3:a:3\? -map 4:a:4\? \
        -f mpegts "udp://224.0.51.1:1234?pkt_size=188"
```

### Camera

Using command, pass a source and destination and the script will generate the ffmpeg command to transcode and send your camera feed.

```bash
docker run -it --rm --gpus=all -e NVIDIA_VISIBLE_DEVICES=all -e NVIDIA_DRIVER_CAPABILITIES=all ghcr.io/aperim/nvidia-cuda-ffmpeg:latest camera \
	rtsp://user:pass@192.0.2.1/unicast/c1/s0/live rtsp://host.docker.internal:8554/camera1 16M h264 24
```

```text
Camera Transcode
        Usage - pass the camera url and the destination
                camera <camera_url> <destination_url> [bitrate] [encoder] [framerate]
                        camera_url The url of your camera - with url encoded usernmame/password if needed
                        destination_url The destination URL
                        bitrate (optional) The output bitrate [default 8M]
                        encoder (optional) Either h264 or hevc [default hevc]
                        framerate (optional) The frame rate [no default]
        Example
                camera rtsp://admin@passw0rd:192.0.2.1/main/0 rtsp://streaming.server.example/camera1 16M h264 24
        Note
                The output url must be RTSP. It will be using TCP
```
