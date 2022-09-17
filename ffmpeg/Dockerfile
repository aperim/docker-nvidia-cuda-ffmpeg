ARG CUDA=11.7.1
ARG FLAVOUR=ubuntu22.04
ARG BULDIMAGE=${CUDA}-devel-${FLAVOUR}
ARG RUNIMAGE=${CUDA}-runtime-${FLAVOUR}

FROM nvidia/cuda:${BULDIMAGE} AS builder

ARG FFMPEG_VERSION=5.1

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
            yasm

RUN mkdir -p /opt/src/nv-codec-headers /opt/src/ffmpeg && \
    git clone --depth 1 https://git.videolan.org/git/ffmpeg/nv-codec-headers.git /opt/src/nv-codec-headers && \
    git clone --depth 1 --branch release/${FFMPEG_VERSION} https://git.ffmpeg.org/ffmpeg.git /opt/src/ffmpeg

RUN cd /opt/src/nv-codec-headers && \
    make install

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
    make -j$(nproc)
    
FROM nvidia/cuda:${RUNIMAGE}

RUN echo "/usr/lib/x86_64-linux-gnu" | tee -a /etc/ld.so.conf && \
    ldconfig

COPY --from=builder /opt/src/ffmpeg/ffmpeg /usr/bin/ffmpeg

COPY --from=builder \
    /usr/lib/x86_64-linux-gnu/libaom.so.3 \
    /usr/lib/x86_64-linux-gnu/libapparmor.so.1 \
    /usr/lib/x86_64-linux-gnu/libasound.so.2 \
    /usr/lib/x86_64-linux-gnu/libass.so.9 \
    /usr/lib/x86_64-linux-gnu/libasyncns.so.0 \
    /usr/lib/x86_64-linux-gnu/libavc1394.so.0 \
    /usr/lib/x86_64-linux-gnu/libavcodec.so.58 \
    /opt/src/ffmpeg/libavcodec/libavcodec.so.59 \
    /opt/src/ffmpeg/libavdevice/libavdevice.so.59 \
    /opt/src/ffmpeg/libavfilter/libavfilter.so.8 \
    /opt/src/ffmpeg/libavformat/libavformat.so.59 \
    /usr/lib/x86_64-linux-gnu/libavutil.so.56 \
    /opt/src/ffmpeg/libavutil/libavutil.so.57 \
    /usr/lib/x86_64-linux-gnu/blas \
    /usr/lib/x86_64-linux-gnu/libblkid.so.1 \
    /usr/lib/x86_64-linux-gnu/libbluray.so.2 \
    /usr/lib/x86_64-linux-gnu/libbrotlicommon.so.1 \
    /usr/lib/x86_64-linux-gnu/libbrotlidec.so.1 \
    /usr/lib/x86_64-linux-gnu/libbs2b.so.0 \
    /usr/lib/x86_64-linux-gnu/libbsd.so.0 \
    /usr/lib/x86_64-linux-gnu/libc.so.6 \
    /usr/lib/x86_64-linux-gnu/libcaca.so.0 \
    /usr/lib/x86_64-linux-gnu/libcairo-gobject.so.2 \
    /usr/lib/x86_64-linux-gnu/libcairo.so.2 \
    /usr/lib/x86_64-linux-gnu/libcap.so.2 \
    /usr/lib/x86_64-linux-gnu/libcdio_cdda.so.2 \
    /usr/lib/x86_64-linux-gnu/libcdio_paranoia.so.2 \
    /usr/lib/x86_64-linux-gnu/libcdio.so.19 \
    /usr/lib/x86_64-linux-gnu/libchromaprint.so.1 \
    /usr/lib/x86_64-linux-gnu/libcodec2.so.1.0 \
    /usr/lib/x86_64-linux-gnu/libcom_err.so.2 \
    /usr/lib/x86_64-linux-gnu/libcrypto.so.3 \
    /usr/lib/x86_64-linux-gnu/libdatrie.so.1 \
    /usr/lib/x86_64-linux-gnu/libdav1d.so.5 \
    /usr/lib/x86_64-linux-gnu/libdb-5.3.so \
    /usr/lib/x86_64-linux-gnu/libdbus-1.so.3 \
    /usr/lib/x86_64-linux-gnu/libdc1394.so.25 \
    /usr/lib/x86_64-linux-gnu/libdecor-0.so.0 \
    /usr/lib/x86_64-linux-gnu/libdl.so.2 \
    /usr/lib/x86_64-linux-gnu/libdrm.so.2 \
    /usr/lib/x86_64-linux-gnu/libexpat.so.1 \
    /usr/lib/x86_64-linux-gnu/libffi.so.8 \
    /usr/lib/x86_64-linux-gnu/libFLAC.so.8 \
    /usr/lib/x86_64-linux-gnu/libflite_cmu_us_awb.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_cmu_us_kal.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_cmu_us_kal16.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_cmu_us_rms.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_cmu_us_slt.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_cmulex.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite_usenglish.so.1 \
    /usr/lib/x86_64-linux-gnu/libflite.so.1 \
    /usr/lib/x86_64-linux-gnu/libfontconfig.so.1 \
    /usr/lib/x86_64-linux-gnu/libfreetype.so.6 \
    /usr/lib/x86_64-linux-gnu/libfribidi.so.0 \
    /usr/lib/x86_64-linux-gnu/libgbm.so.1 \
    /usr/lib/x86_64-linux-gnu/libgcc_s.so.1 \
    /usr/lib/x86_64-linux-gnu/libgcrypt.so.20 \
    /usr/lib/x86_64-linux-gnu/libgdk_pixbuf-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libgfortran.so.5 \
    /usr/lib/x86_64-linux-gnu/libgio-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libGL.so.1 \
    /usr/lib/x86_64-linux-gnu/libGLdispatch.so.0 \
    /usr/lib/x86_64-linux-gnu/libglib-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libGLX.so.0 \
    /usr/lib/x86_64-linux-gnu/libgme.so.0 \
    /usr/lib/x86_64-linux-gnu/libgmodule-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libgmp.so.10 \
    /usr/lib/x86_64-linux-gnu/libgnutls.so.30 \
    /usr/lib/x86_64-linux-gnu/libgobject-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libgomp.so.1 \
    /usr/lib/x86_64-linux-gnu/libgraphite2.so.3 \
    /usr/lib/x86_64-linux-gnu/libgsm.so.1 \
    /usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.2 \
    /usr/lib/x86_64-linux-gnu/libharfbuzz.so.0 \
    /usr/lib/x86_64-linux-gnu/libhogweed.so.6 \
    /usr/lib/x86_64-linux-gnu/libicudata.so.70 \
    /usr/lib/x86_64-linux-gnu/libicuuc.so.70 \
    /usr/lib/x86_64-linux-gnu/libidn2.so.0 \
    /usr/lib/x86_64-linux-gnu/libiec61883.so.0 \
    /usr/lib/x86_64-linux-gnu/libjack.so.0 \
    /usr/lib/x86_64-linux-gnu/libjpeg.so.8 \
    /usr/lib/x86_64-linux-gnu/libk5crypto.so.3 \
    /usr/lib/x86_64-linux-gnu/libkeyutils.so.1 \
    /usr/lib/x86_64-linux-gnu/libkrb5.so.3 \
    /usr/lib/x86_64-linux-gnu/libkrb5support.so.0 \
    /usr/lib/x86_64-linux-gnu/lapack \
    /usr/lib/x86_64-linux-gnu/liblilv-0.so.0 \
    /usr/lib/x86_64-linux-gnu/liblz4.so.1 \
    /usr/lib/x86_64-linux-gnu/liblzma.so.5 \
    /usr/lib/x86_64-linux-gnu/libm.so.6 \
    /usr/lib/x86_64-linux-gnu/libmd.so.0 \
    /usr/lib/x86_64-linux-gnu/libmfx.so.1 \
    /usr/lib/x86_64-linux-gnu/libmount.so.1 \
    /usr/lib/x86_64-linux-gnu/libmp3lame.so.0 \
    /usr/lib/x86_64-linux-gnu/libmpg123.so.0 \
    /usr/lib/x86_64-linux-gnu/libmysofa.so.1 \
    /usr/lib/x86_64-linux-gnu/libncursesw.so.6 \
    /usr/lib/x86_64-linux-gnu/libnettle.so.8 \
    /usr/lib/x86_64-linux-gnu/libnorm.so.1 \
    /usr/lib/x86_64-linux-gnu/libnuma.so.1 \
    /usr/lib/x86_64-linux-gnu/libogg.so.0 \
    /usr/lib/x86_64-linux-gnu/libopenal.so.1 \
    /usr/lib/x86_64-linux-gnu/libopenjp2.so.7 \
    /usr/lib/x86_64-linux-gnu/libopenmpt.so.0 \
    /usr/lib/x86_64-linux-gnu/libopus.so.0 \
    /usr/lib/x86_64-linux-gnu/libp11-kit.so.0 \
    /usr/lib/x86_64-linux-gnu/libpango-1.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libpangocairo-1.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libpangoft2-1.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libpcre.so.3 \
    /usr/lib/x86_64-linux-gnu/libpcre2-8.so.0 \
    /usr/lib/x86_64-linux-gnu/libpgm-5.3.so.0 \
    /usr/lib/x86_64-linux-gnu/libpixman-1.so.0 \
    /usr/lib/x86_64-linux-gnu/libpng16.so.16 \
    /usr/lib/x86_64-linux-gnu/libpocketsphinx.so.3 \
    /opt/src/ffmpeg/libpostproc/libpostproc.so.56 \
    /usr/lib/x86_64-linux-gnu/libpthread.so.0 \
    /usr/lib/x86_64-linux-gnu/libpulse.so.0 \
    /usr/lib/x86_64-linux-gnu/libquadmath.so.0 \
    /usr/lib/x86_64-linux-gnu/librabbitmq.so.4 \
    /usr/lib/x86_64-linux-gnu/libraw1394.so.11 \
    /usr/lib/x86_64-linux-gnu/libresolv.so.2 \
    /usr/lib/x86_64-linux-gnu/librom1394.so.0 \
    /usr/lib/x86_64-linux-gnu/librsvg-2.so.2 \
    /usr/lib/x86_64-linux-gnu/librt.so.1 \
    /usr/lib/x86_64-linux-gnu/librubberband.so.2 \
    /usr/lib/x86_64-linux-gnu/libsamplerate.so.0 \
    /usr/lib/x86_64-linux-gnu/libSDL2-2.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libselinux.so.1 \
    /usr/lib/x86_64-linux-gnu/libserd-0.so.0 \
    /usr/lib/x86_64-linux-gnu/libshine.so.3 \
    /usr/lib/x86_64-linux-gnu/libslang.so.2 \
    /usr/lib/x86_64-linux-gnu/libsnappy.so.1 \
    /usr/lib/x86_64-linux-gnu/libsndfile.so.1 \
    /usr/lib/x86_64-linux-gnu/libsndio.so.7 \
    /usr/lib/x86_64-linux-gnu/libsodium.so.23 \
    /usr/lib/x86_64-linux-gnu/libsord-0.so.0 \
    /usr/lib/x86_64-linux-gnu/libsoxr.so.0 \
    /usr/lib/x86_64-linux-gnu/libspeex.so.1 \
    /usr/lib/x86_64-linux-gnu/libsphinxbase.so.3 \
    /usr/lib/x86_64-linux-gnu/libsratom-0.so.0 \
    /usr/lib/x86_64-linux-gnu/libsrt-gnutls.so.1.4 \
    /usr/lib/x86_64-linux-gnu/libssh.so.4 \
    /usr/lib/x86_64-linux-gnu/libssl.so.3 \
    /usr/lib/x86_64-linux-gnu/libstdc++.so.6 \
    /usr/lib/x86_64-linux-gnu/libswresample.so.3 \
    /opt/src/ffmpeg/libswresample/libswresample.so.4 \
    /opt/src/ffmpeg/libswscale/libswscale.so.6 \
    /usr/lib/x86_64-linux-gnu/libsystemd.so.0 \
    /usr/lib/x86_64-linux-gnu/libtasn1.so.6 \
    /usr/lib/x86_64-linux-gnu/libthai.so.0 \
    /usr/lib/x86_64-linux-gnu/libtheoradec.so.1 \
    /usr/lib/x86_64-linux-gnu/libtheoraenc.so.1 \
    /usr/lib/x86_64-linux-gnu/libtinfo.so.6 \
    /usr/lib/x86_64-linux-gnu/libtwolame.so.0 \
    /usr/lib/x86_64-linux-gnu/libudev.so.1 \
    /usr/lib/x86_64-linux-gnu/libudfread.so.0 \
    /usr/lib/x86_64-linux-gnu/libunistring.so.2 \
    /usr/lib/x86_64-linux-gnu/libusb-1.0.so.0 \
    /usr/lib/x86_64-linux-gnu/libuuid.so.1 \
    /usr/lib/x86_64-linux-gnu/libva-drm.so.2 \
    /usr/lib/x86_64-linux-gnu/libva-x11.so.2 \
    /usr/lib/x86_64-linux-gnu/libva.so.2 \
    /usr/lib/x86_64-linux-gnu/libvdpau.so.1 \
    /usr/lib/x86_64-linux-gnu/libvidstab.so.1.1 \
    /usr/lib/x86_64-linux-gnu/libvorbis.so.0 \
    /usr/lib/x86_64-linux-gnu/libvorbisenc.so.2 \
    /usr/lib/x86_64-linux-gnu/libvorbisfile.so.3 \
    /usr/lib/x86_64-linux-gnu/libvpx.so.7 \
    /usr/lib/x86_64-linux-gnu/libwayland-client.so.0 \
    /usr/lib/x86_64-linux-gnu/libwayland-cursor.so.0 \
    /usr/lib/x86_64-linux-gnu/libwayland-egl.so.1 \
    /usr/lib/x86_64-linux-gnu/libwayland-server.so.0 \
    /usr/lib/x86_64-linux-gnu/libwebp.so.7 \
    /usr/lib/x86_64-linux-gnu/libwebpmux.so.3 \
    /usr/lib/x86_64-linux-gnu/libX11-xcb.so.1 \
    /usr/lib/x86_64-linux-gnu/libX11.so.6 \
    /usr/lib/x86_64-linux-gnu/libx264.so.163 \
    /usr/lib/x86_64-linux-gnu/libx265.so.199 \
    /usr/lib/x86_64-linux-gnu/libXau.so.6 \
    /usr/lib/x86_64-linux-gnu/libxcb-render.so.0 \
    /usr/lib/x86_64-linux-gnu/libxcb-shm.so.0 \
    /usr/lib/x86_64-linux-gnu/libxcb.so.1 \
    /usr/lib/x86_64-linux-gnu/libXcursor.so.1 \
    /usr/lib/x86_64-linux-gnu/libXdmcp.so.6 \
    /usr/lib/x86_64-linux-gnu/libXext.so.6 \
    /usr/lib/x86_64-linux-gnu/libXfixes.so.3 \
    /usr/lib/x86_64-linux-gnu/libXi.so.6 \
    /usr/lib/x86_64-linux-gnu/libXinerama.so.1 \
    /usr/lib/x86_64-linux-gnu/libxkbcommon.so.0 \
    /usr/lib/x86_64-linux-gnu/libxml2.so.2 \
    /usr/lib/x86_64-linux-gnu/libXrandr.so.2 \
    /usr/lib/x86_64-linux-gnu/libXrender.so.1 \
    /usr/lib/x86_64-linux-gnu/libXss.so.1 \
    /usr/lib/x86_64-linux-gnu/libXv.so.1 \
    /usr/lib/x86_64-linux-gnu/libxvidcore.so.4 \
    /usr/lib/x86_64-linux-gnu/libXxf86vm.so.1 \
    /usr/lib/x86_64-linux-gnu/libz.so.1 \
    /usr/lib/x86_64-linux-gnu/libzimg.so.2 \
    /usr/lib/x86_64-linux-gnu/libzmq.so.5 \
    /usr/lib/x86_64-linux-gnu/libzstd.so.1 \
    /usr/lib/x86_64-linux-gnu/libzvbi.so.0 \
    /lib/x86_64-linux-gnu

COPY --from=builder \
    /usr/lib/x86_64-linux-gnu/pulseaudio/libpulsecommon-15.99.so \
    /usr/lib/x86_64-linux-gnu/pulseaudio/

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