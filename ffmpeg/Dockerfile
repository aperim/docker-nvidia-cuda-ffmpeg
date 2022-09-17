ARG CUDA=11.7.1
ARG FLAVOUR=ubuntu22.04
ARG BULDIMAGE=${CUDA}-devel-${FLAVOUR}
ARG RUNIMAGE=${CUDA}-runtime-${FLAVOUR}

FROM nvidia/cuda:${BULDIMAGE} AS builder

RUN apt-get update && \
    apt-get -y --allow-change-held-packages full-upgrade && \
    apt-get -y install \
        build-essential \
            cmake \
            curl \
            flite1-dev \
            frei0r-plugins-dev \
            git \
            ladspa-sdk \
            libaom-dev \
            libass-dev \
            libavc1394-dev \
            libbluray-dev \
            libbs2b-dev \
            libc6 \
            libc6-dev \
            libcaca-dev \
            libcdio-dev \
            libcdio-paranoia-dev \
            libcdparanoia-dev \
            libchromaprint-dev \
            libcodec2-dev \
            libdav1d-dev \
            libdc1394-dev \
            libdrm-dev \
            libgles2-mesa-dev \
            libgme-dev \
            libgnutls28-dev \
            libgsm1-dev \
            libiec16022-dev \
            libiec61883-dev \
            libjack-dev \
            liblilv-dev \
            libmfx-dev \
            libmp3lame-dev \
            libmysofa-dev \
            libnettle8 \
            libnuma-dev \
            libnuma1 \
            libomxil-bellagio-dev \
            libopenal-dev \
            libopengl-dev \
            libopenjp2-7-dev \
            libopenmpt-dev \
            libopus-dev \
            libpocketsphinx-dev \
            libpulse-dev \
            librabbitmq-dev \
            librsvg2-dev \
            librubberband-dev \
            libsdl2-gfx-dev \
            libshine-dev \
            libsnappy-dev \
            libsoxr-dev \
            libspeex-dev \
            libsrt-gnutls-dev \
            libssh-dev \
            libtheora-dev \
            libtool \
            libtwolame-dev \
            libunistring-dev \
            libvidstab-dev \
            libvpx-dev \
            libwebp-dev \
            libx264-dev \
            libx265-dev \
            libxvidcore-dev \
            libzimg-dev \
            libzmq3-dev \
            libzvbi-dev \
            lzip \
            pkg-config \
            unzip \
            wget \
            yasm && \
    mkdir -p /opt/src/nv-codec-headers /opt/src/ffmpeg && \
    git clone --depth 1 https://git.videolan.org/git/ffmpeg/nv-codec-headers.git /opt/src/nv-codec-headers && \
    git clone --depth 1 https://git.ffmpeg.org/ffmpeg.git /opt/src/ffmpeg

RUN cd /opt/src/nv-codec-headers && \
    make install

# RUN cd /opt/src/gmp* && \
#     ./configure --prefix=/usr && \
#     make && \
#     make check && \
#     make install && \
#     cp /usr/include/gnutls/. /usr/include/x86_64-linux-gnu

# for later maybe
# --pkg-config-flags="--static" \


RUN cd /opt/src/ffmpeg && \
    LIBGNUTLS_CFLAGS=-I/usr/include/gnutls ./configure --prefix=/usr \
	--extra-version=0ubuntu0.22.04.1 \
    --extra-cflags=-I/usr/local/cuda/include \
    --extra-ldflags=-L/usr/local/cuda/lib64 \
	--toolchain=hardened \
	--libdir=/usr/lib/x86_64-linux-gnu \
	--incdir=/usr/include/x86_64-linux-gnu \
	--arch=amd64 \
	--enable-gpl \
	--disable-stripping \
    --enable-cuda-nvcc \
	--enable-gnutls \
	--enable-ladspa \
	--enable-libaom \
	--enable-libass \
	--enable-libbluray \
	--enable-libbs2b \
	--enable-libcaca \
	--enable-libcdio \
	--enable-libcodec2 \
	--enable-libdav1d \
	--enable-libflite \
	--enable-libfontconfig \
	--enable-libfreetype \
	--enable-libfribidi \
	--enable-libgme \
	--enable-libgsm \
	--enable-libjack \
	--enable-libmp3lame \
	--enable-libmysofa \
    --enable-libnpp \
	--enable-libopenjpeg \
	--enable-libopenmpt \
	--enable-libopus \
	--enable-libpulse \
	--enable-librabbitmq \
	--enable-librubberband \
	--enable-libshine \
	--enable-libsnappy \
	--enable-libsoxr \
	--enable-libspeex \
	--enable-libsrt \
	--enable-libssh \
	--enable-libtheora \
	--enable-libtwolame \
	--enable-libvidstab \
	--enable-libvorbis \
	--enable-libvpx \
	--enable-libwebp \
	--enable-libx265 \
	--enable-libxml2 \
	--enable-libxvid \
	--enable-libzimg \
	--enable-libzmq \
	--enable-libzvbi \
	--enable-lv2 \
    --enable-nonfree \
    --enable-nvenc \
	--enable-omx \
	--enable-openal \
	--enable-opencl \
	--enable-opengl \
	--enable-sdl2 \
	--enable-pocketsphinx \
	--enable-librsvg \
	--enable-libmfx \
	--enable-libdc1394 \
	--enable-libdrm \
	--enable-libiec61883 \
	--enable-chromaprint \
	--enable-frei0r \
	--enable-libx264 \
	--enable-shared; \
    cat ffbuild/config.log || true && \
    make -j$(nproc) && \
    make install

# RUN find /usr/include -mmin +59 -print0 | xargs -0 tar cfz /opt/src/include.tgz && \
#     find /usr/lib -mmin +59 -print0 | xargs -0 tar cfz /opt/src/lib.tgz
    
FROM nvidia/cuda:${RUNIMAGE}

RUN apt-get update && \
    apt-get install -y make gcc && \
    apt-get clean autoclean && \
    apt-get autoremove --yes && \
    rm -rf /var/lib/{apt,dpkg,cache,log}/

# COPY --from=builder /opt/src/include.tgz /opt/src/lib.tgz /opt/src/
COPY --from=builder /usr/bin/ffmpeg /usr/bin/ffmpeg

RUN echo "/usr/lib/x86_64-linux-gnu" | tee -a /etc/ld.so.conf && \
    ldconfig

COPY --from=builder /usr/include/x86_64-linux-gnu/libavutil /usr/include/x86_64-linux-gnu/libavutil
COPY --from=builder /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu
COPY --from=builder /opt/src/ffmpeg /opt/src/ffmpeg
COPY --from=builder /usr/include/libdrm /usr/include/libdrm
COPY --from=builder /usr/include/drm /usr/include/drm
COPY --from=builder /usr/include/GL /usr/include/GL
COPY --from=builder /usr/include/GLES /usr/include/GLES
COPY --from=builder /usr/include/KHR /usr/include/KHR
COPY --from=builder /usr/include/X11 /usr/include/X11
COPY --from=builder /usr/include/SDL2 /usr/include/SDL2
COPY --from=builder /usr/include/bs2b /usr/include/bs2b
COPY --from=builder /usr/include/lilv-0 /usr/include/lilv-0
COPY --from=builder /usr/include/freetype2 /usr/include/freetype2
COPY --from=builder /usr/local/cuda-11.7/targets/x86_64-linux/include /usr/local/cuda-11.7/targets/x86_64-linux/include
COPY --from=builder /usr/include/mfx /usr/include/mfx
COPY --from=builder /usr/include/fontconfig /usr/include/fontconfig/
COPY --from=builder /usr/include/fribidi /usr/include/fribidi
COPY --from=builder /usr/include/libyasm /usr/include/libyasm
COPY --from=builder /usr/include/libxml2 /usr/include/libxml2
COPY --from=builder /usr/include/openjpeg-2.4 /usr/include/openjpeg-2.4
COPY --from=builder /usr/include/opus /usr/include/opus

RUN rm -f /usr/lib/x86_64-linux-gnu/liblapack.so.3 && \
    rm -f /usr/lib/x86_64-linux-gnu/libblas.so.3 && \
    ln -s /usr/lib/x86_64-linux-gnu/blas/libblas.so.3 /usr/lib/x86_64-linux-gnu/libblas.so.3 && \
    ln -s /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3 /usr/lib/x86_64-linux-gnu/liblapack.so.3 && \
    cd /opt/src/ffmpeg && \
    make install

ARG BUILD_DESCRIPTION="nvidia cuda ffmpeg"
ARG BUILD_NAME="nvidia cuda ffmpeg"
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

LABEL \
    maintainer="Troy Kelly <troy@aperim.com>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Troy Kelly" \
    org.opencontainers.image.authors="Troy Kelly <troy@aperim.com>" \
    org.opencontainers.image.licenses="Apache-2.0" \
    org.opencontainers.image.url="https://aperim.com" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}