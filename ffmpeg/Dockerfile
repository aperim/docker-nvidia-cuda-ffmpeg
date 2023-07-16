ARG CUDA=12.2.0
ARG OS=ubuntu22.04
ARG BULDIMAGE=${CUDA}-devel-${OS}
ARG RUNIMAGE=${CUDA}-runtime-${OS}

FROM nvidia/cuda:${BULDIMAGE} AS builder
ARG CUDA
ARG OS

RUN apt-get update && \
	apt-get -y --allow-change-held-packages full-upgrade && \
	DEBIAN_FRONTEND=noninteractive apt-get -qqy install \
	automake \
	autoconf \
	bc \
	build-essential \
	cmake \
	curl \
	cython3 \
	devscripts \
	equivs \
	flite1-dev \
	frei0r-plugins-dev \
	git \
	imagemagick \
	intltool \
	ladspa-sdk \
	libaom-dev \
	libass-dev \
	libaribb24-dev \
	libdavs2-dev \
	liblcms2-dev \
	libopencv-dev \
	libsctp-dev \
	freeglut3-dev \
	libavc1394-dev \
	libavutil-dev \
	libavcodec-dev \
	libswscale-dev \
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
	libfdk-aac-dev \
	libgcrypt20-dev \
	libgles2-mesa-dev \
	libgme-dev \
	libgnutls28-dev \
	libgsm1-dev \
	libiec16022-dev \
	libiec61883-dev \
	libjack-dev \
	liblcms2-dev \
	liblensfun-dev \
	liblilv-dev \
	libmfx-dev \
	libopencore-amrnb-dev \
	libopencore-amrwb-dev \
	libopenh264-dev \
	libopenmpt-modplug-dev \
	libplacebo-dev \
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
	librtmp-dev \
	librubberband-dev \
	libsdl2-gfx-dev \
	libshine-dev \
	libsmbclient-dev \
	libsnappy-dev \
	libsoxr-dev \
	libspeex-dev \
	libsrt-gnutls-dev \
	libssh-dev \
	libtesseract-dev \
	libtheora-dev \
	libtool \
	libtwolame-dev \
	libunistring-dev \
	libvidstab-dev \
	libvo-amrwbenc-dev \
	libvpx-dev \
	libwebp-dev \
	libx264-dev \
	libx265-dev \
	libxavs2-dev \
	libxcb-shape0-dev \
	libxcb-xfixes0-dev \
	libxvidcore-dev \
	libzimg-dev \
	libzmq3-dev \
	libzvbi-dev \
	lzip \
	pkg-config \
	python3-dev \
	python3-pip \
	samba-dev \
	unzip \
	wget \
	yasm

ARG FFMPEG_VERSION=6.0
ARG AVISYNTHPLUS_VERSION=3.7.2
ARG NVCODEC_HEADERS_VERSION=12.0.16.0

RUN rm -Rf /opt/src/* && \
	mkdir -p /opt/src/nv-codec-headers /opt/src/ffmpeg /opt/src/vapoursynth && \
	git clone --depth 1 --branch n${NVCODEC_HEADERS_VERSION} https://git.videolan.org/git/ffmpeg/nv-codec-headers.git /opt/src/nv-codec-headers && \
	git clone --depth 1 --branch n${FFMPEG_VERSION} https://git.ffmpeg.org/ffmpeg.git /opt/src/ffmpeg && \
	git clone --depth 1 https://github.com/vapoursynth/vapoursynth.git /opt/src/vapoursynth && \
	curl -L https://github.com/AviSynth/AviSynthPlus/archive/refs/tags/v${AVISYNTHPLUS_VERSION}.tar.gz | tar -xz -C /opt/src && \
	cd /opt/src/nv-codec-headers && \
	make && \
	make install && \
	cd /opt/src/vapoursynth && \
	pip3 install -r ./python-requirements.txt && \
	./autogen.sh && \
	LIBGNUTLS_CFLAGS=-I/usr/include/gnutls ./configure --prefix=/usr && \
	make -j$(nproc) && \
	make install && \
	cd /opt/src/AviSynthPlus-${AVISYNTHPLUS_VERSION} && \
	mkdir build && \
	cd build && \
	cmake -DCMAKE_INSTALL_PREFIX=/usr .. && \
	make -j$(nproc) && \
	make install

# for later maybe
# --pkg-config-flags="--static" \

# Build ffmpeg here
ARG FFMPEG_CONFIG_FLAGS="--enable-cuvid --enable-avisynth --enable-bsf=aac_adtstoasc --enable-bsf=av1_frame_merge --enable-bsf=av1_frame_split --enable-bsf=av1_metadata --enable-bsf=chomp --enable-bsf=dca_core --enable-bsf=dts2pts --enable-bsf=dump_extradata --enable-bsf=dv_error_marker --enable-bsf=eac3_core --enable-bsf=extract_extradata --enable-bsf=filter_units --enable-bsf=h264_metadata --enable-bsf=h264_mp4toannexb --enable-bsf=h264_redundant_pps --enable-bsf=hapqa_extract --enable-bsf=hevc_metadata --enable-bsf=hevc_mp4toannexb --enable-bsf=imx_dump_header --enable-bsf=media100_to_mjpegb --enable-bsf=mjpeg2jpeg --enable-bsf=mjpega_dump_header --enable-bsf=mov2textsub --enable-bsf=mp3_header_decompress --enable-bsf=mpeg2_metadata --enable-bsf=mpeg4_unpack_bframes --enable-bsf=noise --enable-bsf=null --enable-bsf=opus_metadata --enable-bsf=pcm_rechunk --enable-bsf=pgs_frame_merge --enable-bsf=prores_metadata --enable-bsf=remove_extradata --enable-bsf=setts --enable-bsf=text2movsub --enable-bsf=trace_headers --enable-bsf=truehd_core --enable-bsf=vp9_metadata --enable-bsf=vp9_raw_reorder --enable-bsf=vp9_superframe --enable-bsf=vp9_superframe_split --enable-chromaprint --enable-cuda-nvcc --enable-decoder=aac --enable-decoder=aac_fixed --enable-decoder=aac_latm --enable-decoder=aasc --enable-decoder=ac3 --enable-decoder=ac3_fixed --enable-decoder=acelp_kelvin --enable-decoder=adpcm_4xm --enable-decoder=adpcm_adx --enable-decoder=adpcm_afc --enable-decoder=adpcm_agm --enable-decoder=adpcm_aica --enable-decoder=adpcm_argo --enable-decoder=adpcm_ct --enable-decoder=adpcm_dtk --enable-decoder=adpcm_ea --enable-decoder=adpcm_ea_maxis_xa --enable-decoder=adpcm_ea_r1 --enable-decoder=adpcm_ea_r2 --enable-decoder=adpcm_ea_r3 --enable-decoder=adpcm_ea_xas --enable-decoder=adpcm_g722 --enable-decoder=adpcm_g726 --enable-decoder=adpcm_g726le --enable-decoder=adpcm_ima_acorn --enable-decoder=adpcm_ima_alp --enable-decoder=adpcm_ima_amv --enable-decoder=adpcm_ima_apc --enable-decoder=adpcm_ima_apm --enable-decoder=adpcm_ima_cunning --enable-decoder=adpcm_ima_dat4 --enable-decoder=adpcm_ima_dk3 --enable-decoder=adpcm_ima_dk4 --enable-decoder=adpcm_ima_ea_eacs --enable-decoder=adpcm_ima_ea_sead --enable-decoder=adpcm_ima_iss --enable-decoder=adpcm_ima_moflex --enable-decoder=adpcm_ima_mtf --enable-decoder=adpcm_ima_oki --enable-decoder=adpcm_ima_qt --enable-decoder=adpcm_ima_rad --enable-decoder=adpcm_ima_smjpeg --enable-decoder=adpcm_ima_ssi --enable-decoder=adpcm_ima_wav --enable-decoder=adpcm_ima_ws --enable-decoder=adpcm_ms --enable-decoder=adpcm_mtaf --enable-decoder=adpcm_psx --enable-decoder=adpcm_sbpro_2 --enable-decoder=adpcm_sbpro_3 --enable-decoder=adpcm_sbpro_4 --enable-decoder=adpcm_swf --enable-decoder=adpcm_thp --enable-decoder=adpcm_thp_le --enable-decoder=adpcm_vima --enable-decoder=adpcm_xa --enable-decoder=adpcm_xmd --enable-decoder=adpcm_yamaha --enable-decoder=adpcm_zork --enable-decoder=agm --enable-decoder=aic --enable-decoder=alac --enable-decoder=alias_pix --enable-decoder=als --enable-decoder=amrnb --enable-decoder=amrwb --enable-decoder=amv --enable-decoder=anm --enable-decoder=ansi --enable-decoder=anull --enable-decoder=apac --enable-decoder=ape --enable-decoder=apng --enable-decoder=aptx --enable-decoder=aptx_hd --enable-decoder=arbc --enable-decoder=argo --enable-decoder=ass --enable-decoder=asv1 --enable-decoder=asv2 --enable-decoder=atrac1 --enable-decoder=atrac3 --enable-decoder=atrac3al --enable-decoder=atrac3p --enable-decoder=atrac3pal --enable-decoder=atrac9 --enable-decoder=aura --enable-decoder=aura2 --enable-decoder=av1 --enable-decoder=av1_cuvid --enable-decoder=avrn --enable-decoder=avrp --enable-decoder=avs --enable-decoder=avui --enable-decoder=ayuv --enable-decoder=bethsoftvid --enable-decoder=bfi --enable-decoder=bink --enable-decoder=binkaudio_dct --enable-decoder=binkaudio_rdft --enable-decoder=bintext --enable-decoder=bitpacked --enable-decoder=bmp --enable-decoder=bmv_audio --enable-decoder=bmv_video --enable-decoder=bonk --enable-decoder=brender_pix --enable-decoder=c93 --enable-decoder=cavs --enable-decoder=cbd2_dpcm --enable-decoder=ccaption --enable-decoder=cdgraphics --enable-decoder=cdtoons --enable-decoder=cdxl --enable-decoder=cfhd --enable-decoder=cinepak --enable-decoder=clearvideo --enable-decoder=cljr --enable-decoder=cllc --enable-decoder=comfortnoise --enable-decoder=cook --enable-decoder=cpia --enable-decoder=cri --enable-decoder=cscd --enable-decoder=cyuv --enable-decoder=dca --enable-decoder=dds --enable-decoder=derf_dpcm --enable-decoder=dfa --enable-decoder=dfpwm --enable-decoder=dirac --enable-decoder=dnxhd --enable-decoder=dolby_e --enable-decoder=dpx --enable-decoder=dsd_lsbf --enable-decoder=dsd_lsbf_planar --enable-decoder=dsd_msbf --enable-decoder=dsd_msbf_planar --enable-decoder=dsicinaudio --enable-decoder=dsicinvideo --enable-decoder=dss_sp --enable-decoder=dst --enable-decoder=dvaudio --enable-decoder=dvbsub --enable-decoder=dvdsub --enable-decoder=dvvideo --enable-decoder=dxa --enable-decoder=dxtory --enable-decoder=dxv --enable-decoder=eac3 --enable-decoder=eacmv --enable-decoder=eamad --enable-decoder=eatgq --enable-decoder=eatgv --enable-decoder=eatqi --enable-decoder=eightbps --enable-decoder=eightsvx_exp --enable-decoder=eightsvx_fib --enable-decoder=escape124 --enable-decoder=escape130 --enable-decoder=evrc --enable-decoder=exr --enable-decoder=fastaudio --enable-decoder=ffv1 --enable-decoder=ffvhuff --enable-decoder=ffwavesynth --enable-decoder=fic --enable-decoder=fits --enable-decoder=flac --enable-decoder=flashsv --enable-decoder=flashsv2 --enable-decoder=flic --enable-decoder=flv --enable-decoder=fmvc --enable-decoder=fourxm --enable-decoder=fraps --enable-decoder=frwu --enable-decoder=ftr --enable-decoder=g2m --enable-decoder=g723_1 --enable-decoder=g729 --enable-decoder=gdv --enable-decoder=gem --enable-decoder=gif --enable-decoder=gremlin_dpcm --enable-decoder=gsm --enable-decoder=gsm_ms --enable-decoder=h261 --enable-decoder=h263 --enable-decoder=h263_v4l2m2m --enable-decoder=h263i --enable-decoder=h263p --enable-decoder=h264 --enable-decoder=h264_cuvid --enable-decoder=h264_v4l2m2m --enable-decoder=hap --enable-decoder=hca --enable-decoder=hcom --enable-decoder=hdr --enable-decoder=hevc --enable-decoder=hevc_cuvid --enable-decoder=hevc_v4l2m2m --enable-decoder=hnm4_video --enable-decoder=hq_hqa --enable-decoder=hqx --enable-decoder=huffyuv --enable-decoder=hymt --enable-decoder=iac --enable-decoder=idcin --enable-decoder=idf --enable-decoder=iff_ilbm --enable-decoder=ilbc --enable-decoder=imc --enable-decoder=imm4 --enable-decoder=imm5 --enable-decoder=indeo2 --enable-decoder=indeo3 --enable-decoder=indeo4 --enable-decoder=indeo5 --enable-decoder=interplay_acm --enable-decoder=interplay_dpcm --enable-decoder=interplay_video --enable-decoder=ipu --enable-decoder=jacosub --enable-decoder=jpeg2000 --enable-decoder=jpegls --enable-decoder=jv --enable-decoder=kgv1 --enable-decoder=kmvc --enable-decoder=lagarith --enable-decoder=libaom_av1 --enable-decoder=libaribb24 --enable-decoder=libcodec2 --enable-decoder=libdav1d --enable-decoder=libdavs2 --enable-decoder=libfdk_aac --enable-decoder=libgsm --enable-decoder=libgsm_ms --enable-decoder=libopencore_amrnb --enable-decoder=libopencore_amrwb --enable-decoder=libopenh264 --enable-decoder=libopenjpeg --enable-decoder=libopus --enable-decoder=librsvg --enable-decoder=libspeex --enable-decoder=libuavs3d --enable-decoder=libvorbis --enable-decoder=libvpx_vp8 --enable-decoder=libvpx_vp9 --enable-decoder=libzvbi_teletext --enable-decoder=loco --enable-decoder=lscr --enable-decoder=m101 --enable-decoder=mace3 --enable-decoder=mace6 --enable-decoder=magicyuv --enable-decoder=mdec --enable-decoder=media100 --enable-decoder=metasound --enable-decoder=microdvd --enable-decoder=mimic --enable-decoder=misc4 --enable-decoder=mjpeg --enable-decoder=mjpeg_cuvid --enable-decoder=mjpegb --enable-decoder=mlp --enable-decoder=mmvideo --enable-decoder=mobiclip --enable-decoder=motionpixels --enable-decoder=movtext --enable-decoder=mp1 --enable-decoder=mp1float --enable-decoder=mp2 --enable-decoder=mp2float --enable-decoder=mp3 --enable-decoder=mp3adu --enable-decoder=mp3adufloat --enable-decoder=mp3float --enable-decoder=mp3on4 --enable-decoder=mp3on4float --enable-decoder=mpc7 --enable-decoder=mpc8 --enable-decoder=mpeg1_cuvid --enable-decoder=mpeg1_v4l2m2m --enable-decoder=mpeg1video --enable-decoder=mpeg2_cuvid --enable-decoder=mpeg2_v4l2m2m --enable-decoder=mpeg2video --enable-decoder=mpeg4 --enable-decoder=mpeg4_cuvid --enable-decoder=mpeg4_v4l2m2m --enable-decoder=mpegvideo --enable-decoder=mpl2 --enable-decoder=msa1 --enable-decoder=mscc --enable-decoder=msmpeg4v1 --enable-decoder=msmpeg4v2 --enable-decoder=msmpeg4v3 --enable-decoder=msnsiren --enable-decoder=msp2 --enable-decoder=msrle --enable-decoder=mss1 --enable-decoder=mss2 --enable-decoder=msvideo1 --enable-decoder=mszh --enable-decoder=mts2 --enable-decoder=mv30 --enable-decoder=mvc1 --enable-decoder=mvc2 --enable-decoder=mvdv --enable-decoder=mvha --enable-decoder=mwsc --enable-decoder=mxpeg --enable-decoder=nellymoser --enable-decoder=notchlc --enable-decoder=nuv --enable-decoder=on2avc --enable-decoder=opus --enable-decoder=paf_audio --enable-decoder=paf_video --enable-decoder=pam --enable-decoder=pbm --enable-decoder=pcm_alaw --enable-decoder=pcm_bluray --enable-decoder=pcm_dvd --enable-decoder=pcm_f16le --enable-decoder=pcm_f24le --enable-decoder=pcm_f32be --enable-decoder=pcm_f32le --enable-decoder=pcm_f64be --enable-decoder=pcm_f64le --enable-decoder=pcm_lxf --enable-decoder=pcm_mulaw --enable-decoder=pcm_s16be --enable-decoder=pcm_s16be_planar --enable-decoder=pcm_s16le --enable-decoder=pcm_s16le_planar --enable-decoder=pcm_s24be --enable-decoder=pcm_s24daud --enable-decoder=pcm_s24le --enable-decoder=pcm_s24le_planar --enable-decoder=pcm_s32be --enable-decoder=pcm_s32le --enable-decoder=pcm_s32le_planar --enable-decoder=pcm_s64be --enable-decoder=pcm_s64le --enable-decoder=pcm_s8 --enable-decoder=pcm_s8_planar --enable-decoder=pcm_sga --enable-decoder=pcm_u16be --enable-decoder=pcm_u16le --enable-decoder=pcm_u24be --enable-decoder=pcm_u24le --enable-decoder=pcm_u32be --enable-decoder=pcm_u32le --enable-decoder=pcm_u8 --enable-decoder=pcm_vidc --enable-decoder=pcx --enable-decoder=pfm --enable-decoder=pgm --enable-decoder=pgmyuv --enable-decoder=pgssub --enable-decoder=pgx --enable-decoder=phm --enable-decoder=photocd --enable-decoder=pictor --enable-decoder=pixlet --enable-decoder=pjs --enable-decoder=png --enable-decoder=ppm --enable-decoder=prores --enable-decoder=prosumer --enable-decoder=psd --enable-decoder=ptx --enable-decoder=qcelp --enable-decoder=qdm2 --enable-decoder=qdmc --enable-decoder=qdraw --enable-decoder=qoi --enable-decoder=qpeg --enable-decoder=qtrle --enable-decoder=r10k --enable-decoder=r210 --enable-decoder=ra_144 --enable-decoder=ra_288 --enable-decoder=ralf --enable-decoder=rasc --enable-decoder=rawvideo --enable-decoder=realtext --enable-decoder=rka --enable-decoder=rl2 --enable-decoder=roq --enable-decoder=roq_dpcm --enable-decoder=rpza --enable-decoder=rscc --enable-decoder=rv10 --enable-decoder=rv20 --enable-decoder=rv30 --enable-decoder=rv40 --enable-decoder=s302m --enable-decoder=sami --enable-decoder=sanm --enable-decoder=sbc --enable-decoder=scpr --enable-decoder=screenpresso --enable-decoder=sdx2_dpcm --enable-decoder=sga --enable-decoder=sgi --enable-decoder=sgirle --enable-decoder=sheervideo --enable-decoder=shorten --enable-decoder=simbiosis_imx --enable-decoder=sipr --enable-decoder=siren --enable-decoder=smackaud --enable-decoder=smacker --enable-decoder=smc --enable-decoder=smvjpeg --enable-decoder=snow --enable-decoder=sol_dpcm --enable-decoder=sonic --enable-decoder=sp5x --enable-decoder=speedhq --enable-decoder=speex --enable-decoder=srgc --enable-decoder=srt --enable-decoder=ssa --enable-decoder=stl --enable-decoder=subrip --enable-decoder=subviewer --enable-decoder=subviewer1 --enable-decoder=sunrast --enable-decoder=svq1 --enable-decoder=svq3 --enable-decoder=tak --enable-decoder=targa --enable-decoder=targa_y216 --enable-decoder=tdsc --enable-decoder=text --enable-decoder=theora --enable-decoder=thp --enable-decoder=tiertexseqvideo --enable-decoder=tiff --enable-decoder=tmv --enable-decoder=truehd --enable-decoder=truemotion1 --enable-decoder=truemotion2 --enable-decoder=truemotion2rt --enable-decoder=truespeech --enable-decoder=tscc --enable-decoder=tscc2 --enable-decoder=tta --enable-decoder=twinvq --enable-decoder=txd --enable-decoder=ulti --enable-decoder=utvideo --enable-decoder=v210 --enable-decoder=v210x --enable-decoder=v308 --enable-decoder=v408 --enable-decoder=v410 --enable-decoder=vb --enable-decoder=vble --enable-decoder=vbn --enable-decoder=vc1 --enable-decoder=vc1_cuvid --enable-decoder=vc1_v4l2m2m --enable-decoder=vc1image --enable-decoder=vcr1 --enable-decoder=vmdaudio --enable-decoder=vmdvideo --enable-decoder=vmnc --enable-decoder=vnull --enable-decoder=vorbis --enable-decoder=vp3 --enable-decoder=vp4 --enable-decoder=vp5 --enable-decoder=vp6 --enable-decoder=vp6a --enable-decoder=vp6f --enable-decoder=vp7 --enable-decoder=vp8 --enable-decoder=vp8_cuvid --enable-decoder=vp8_v4l2m2m --enable-decoder=vp9 --enable-decoder=vp9_cuvid --enable-decoder=vp9_v4l2m2m --enable-decoder=vplayer --enable-decoder=vqa --enable-decoder=vqc --enable-decoder=wady_dpcm --enable-decoder=wavarc --enable-decoder=wavpack --enable-decoder=wbmp --enable-decoder=wcmv --enable-decoder=webp --enable-decoder=webvtt --enable-decoder=wmalossless --enable-decoder=wmapro --enable-decoder=wmav1 --enable-decoder=wmav2 --enable-decoder=wmavoice --enable-decoder=wmv1 --enable-decoder=wmv2 --enable-decoder=wmv3 --enable-decoder=wmv3image --enable-decoder=wnv1 --enable-decoder=wrapped_avframe --enable-decoder=ws_snd1 --enable-decoder=xan_dpcm --enable-decoder=xan_wc3 --enable-decoder=xan_wc4 --enable-decoder=xbin --enable-decoder=xbm --enable-decoder=xface --enable-decoder=xl --enable-decoder=xma1 --enable-decoder=xma2 --enable-decoder=xpm --enable-decoder=xsub --enable-decoder=xwd --enable-decoder=y41p --enable-decoder=ylc --enable-decoder=yop --enable-decoder=yuv4 --enable-decoder=zero12v --enable-decoder=zerocodec --enable-decoder=zlib --enable-decoder=zmbv --enable-demuxer=aa --enable-demuxer=aac --enable-demuxer=aax --enable-demuxer=ac3 --enable-demuxer=ace --enable-demuxer=acm --enable-demuxer=act --enable-demuxer=adf --enable-demuxer=adp --enable-demuxer=ads --enable-demuxer=adx --enable-demuxer=aea --enable-demuxer=afc --enable-demuxer=aiff --enable-demuxer=aix --enable-demuxer=alp --enable-demuxer=amr --enable-demuxer=amrnb --enable-demuxer=amrwb --enable-demuxer=anm --enable-demuxer=apac --enable-demuxer=apc --enable-demuxer=ape --enable-demuxer=apm --enable-demuxer=apng --enable-demuxer=aptx --enable-demuxer=aptx_hd --enable-demuxer=aqtitle --enable-demuxer=argo_asf --enable-demuxer=argo_brp --enable-demuxer=argo_cvg --enable-demuxer=asf --enable-demuxer=asf_o --enable-demuxer=ass --enable-demuxer=ast --enable-demuxer=au --enable-demuxer=av1 --enable-demuxer=avi --enable-demuxer=avisynth --enable-demuxer=avr --enable-demuxer=avs --enable-demuxer=avs2 --enable-demuxer=avs3 --enable-demuxer=bethsoftvid --enable-demuxer=bfi --enable-demuxer=bfstm --enable-demuxer=bink --enable-demuxer=binka --enable-demuxer=bintext --enable-demuxer=bit --enable-demuxer=bitpacked --enable-demuxer=bmv --enable-demuxer=boa --enable-demuxer=bonk --enable-demuxer=brstm --enable-demuxer=c93 --enable-demuxer=caf --enable-demuxer=cavsvideo --enable-demuxer=cdg --enable-demuxer=cdxl --enable-demuxer=cine --enable-demuxer=codec2 --enable-demuxer=codec2raw --enable-demuxer=concat --enable-demuxer=dash --enable-demuxer=data --enable-demuxer=daud --enable-demuxer=dcstr --enable-demuxer=derf --enable-demuxer=dfa --enable-demuxer=dfpwm --enable-demuxer=dhav --enable-demuxer=dirac --enable-demuxer=dnxhd --enable-demuxer=dsf --enable-demuxer=dsicin --enable-demuxer=dss --enable-demuxer=dts --enable-demuxer=dtshd --enable-demuxer=dv --enable-demuxer=dvbsub --enable-demuxer=dvbtxt --enable-demuxer=dxa --enable-demuxer=ea --enable-demuxer=ea_cdata --enable-demuxer=eac3 --enable-demuxer=epaf --enable-demuxer=ffmetadata --enable-demuxer=filmstrip --enable-demuxer=fits --enable-demuxer=flac --enable-demuxer=flic --enable-demuxer=flv --enable-demuxer=fourxm --enable-demuxer=frm --enable-demuxer=fsb --enable-demuxer=fwse --enable-demuxer=g722 --enable-demuxer=g723_1 --enable-demuxer=g726 --enable-demuxer=g726le --enable-demuxer=g729 --enable-demuxer=gdv --enable-demuxer=genh --enable-demuxer=gif --enable-demuxer=gsm --enable-demuxer=gxf --enable-demuxer=h261 --enable-demuxer=h263 --enable-demuxer=h264 --enable-demuxer=hca --enable-demuxer=hcom --enable-demuxer=hevc --enable-demuxer=hls --enable-demuxer=hnm --enable-demuxer=ico --enable-demuxer=idcin --enable-demuxer=idf --enable-demuxer=iff --enable-demuxer=ifv --enable-demuxer=ilbc --enable-demuxer=image_bmp_pipe --enable-demuxer=image_cri_pipe --enable-demuxer=image_dds_pipe --enable-demuxer=image_dpx_pipe --enable-demuxer=image_exr_pipe --enable-demuxer=image_gem_pipe --enable-demuxer=image_gif_pipe --enable-demuxer=image_hdr_pipe --enable-demuxer=image_j2k_pipe --enable-demuxer=image_jpeg_pipe --enable-demuxer=image_jpegls_pipe --enable-demuxer=image_jpegxl_pipe --enable-demuxer=image_pam_pipe --enable-demuxer=image_pbm_pipe --enable-demuxer=image_pcx_pipe --enable-demuxer=image_pfm_pipe --enable-demuxer=image_pgm_pipe --enable-demuxer=image_pgmyuv_pipe --enable-demuxer=image_pgx_pipe --enable-demuxer=image_phm_pipe --enable-demuxer=image_photocd_pipe --enable-demuxer=image_pictor_pipe --enable-demuxer=image_png_pipe --enable-demuxer=image_ppm_pipe --enable-demuxer=image_psd_pipe --enable-demuxer=image_qdraw_pipe --enable-demuxer=image_qoi_pipe --enable-demuxer=image_sgi_pipe --enable-demuxer=image_sunrast_pipe --enable-demuxer=image_svg_pipe --enable-demuxer=image_tiff_pipe --enable-demuxer=image_vbn_pipe --enable-demuxer=image_webp_pipe --enable-demuxer=image_xbm_pipe --enable-demuxer=image_xpm_pipe --enable-demuxer=image_xwd_pipe --enable-demuxer=image2 --enable-demuxer=image2_alias_pix --enable-demuxer=image2_brender_pix --enable-demuxer=image2pipe --enable-demuxer=imf --enable-demuxer=ingenient --enable-demuxer=ipmovie --enable-demuxer=ipu --enable-demuxer=ircam --enable-demuxer=iss --enable-demuxer=iv8 --enable-demuxer=ivf --enable-demuxer=ivr --enable-demuxer=jacosub --enable-demuxer=jv --enable-demuxer=kux --enable-demuxer=kvag --enable-demuxer=laf --enable-demuxer=libgme --enable-demuxer=libmodplug --enable-demuxer=libopenmpt --enable-demuxer=live_flv --enable-demuxer=lmlm4 --enable-demuxer=loas --enable-demuxer=lrc --enable-demuxer=luodat --enable-demuxer=lvf --enable-demuxer=lxf --enable-demuxer=m4v --enable-demuxer=matroska --enable-demuxer=mca --enable-demuxer=mcc --enable-demuxer=mgsts --enable-demuxer=microdvd --enable-demuxer=mjpeg --enable-demuxer=mjpeg_2000 --enable-demuxer=mlp --enable-demuxer=mlv --enable-demuxer=mm --enable-demuxer=mmf --enable-demuxer=mods --enable-demuxer=moflex --enable-demuxer=mov --enable-demuxer=mp3 --enable-demuxer=mpc --enable-demuxer=mpc8 --enable-demuxer=mpegps --enable-demuxer=mpegts --enable-demuxer=mpegtsraw --enable-demuxer=mpegvideo --enable-demuxer=mpjpeg --enable-demuxer=mpl2 --enable-demuxer=mpsub --enable-demuxer=msf --enable-demuxer=msnwc_tcp --enable-demuxer=msp --enable-demuxer=mtaf --enable-demuxer=mtv --enable-demuxer=musx --enable-demuxer=mv --enable-demuxer=mvi --enable-demuxer=mxf --enable-demuxer=mxg --enable-demuxer=nc --enable-demuxer=nistsphere --enable-demuxer=nsp --enable-demuxer=nsv --enable-demuxer=nut --enable-demuxer=nuv --enable-demuxer=obu --enable-demuxer=ogg --enable-demuxer=oma --enable-demuxer=paf --enable-demuxer=pcm_alaw --enable-demuxer=pcm_f32be --enable-demuxer=pcm_f32le --enable-demuxer=pcm_f64be --enable-demuxer=pcm_f64le --enable-demuxer=pcm_mulaw --enable-demuxer=pcm_s16be --enable-demuxer=pcm_s16le --enable-demuxer=pcm_s24be --enable-demuxer=pcm_s24le --enable-demuxer=pcm_s32be --enable-demuxer=pcm_s32le --enable-demuxer=pcm_s8 --enable-demuxer=pcm_u16be --enable-demuxer=pcm_u16le --enable-demuxer=pcm_u24be --enable-demuxer=pcm_u24le --enable-demuxer=pcm_u32be --enable-demuxer=pcm_u32le --enable-demuxer=pcm_u8 --enable-demuxer=pcm_vidc --enable-demuxer=pjs --enable-demuxer=pmp --enable-demuxer=pp_bnk --enable-demuxer=pva --enable-demuxer=pvf --enable-demuxer=qcp --enable-demuxer=r3d --enable-demuxer=rawvideo --enable-demuxer=realtext --enable-demuxer=redspark --enable-demuxer=rka --enable-demuxer=rl2 --enable-demuxer=rm --enable-demuxer=roq --enable-demuxer=rpl --enable-demuxer=rsd --enable-demuxer=rso --enable-demuxer=rtp --enable-demuxer=rtsp --enable-demuxer=s337m --enable-demuxer=sami --enable-demuxer=sap --enable-demuxer=sbc --enable-demuxer=sbg --enable-demuxer=scc --enable-demuxer=scd --enable-demuxer=sdns --enable-demuxer=sdp --enable-demuxer=sdr2 --enable-demuxer=sds --enable-demuxer=sdx --enable-demuxer=segafilm --enable-demuxer=ser --enable-demuxer=sga --enable-demuxer=shorten --enable-demuxer=siff --enable-demuxer=simbiosis_imx --enable-demuxer=sln --enable-demuxer=smacker --enable-demuxer=smjpeg --enable-demuxer=smush --enable-demuxer=sol --enable-demuxer=sox --enable-demuxer=spdif --enable-demuxer=srt --enable-demuxer=stl --enable-demuxer=str --enable-demuxer=subviewer --enable-demuxer=subviewer1 --enable-demuxer=sup --enable-demuxer=svag --enable-demuxer=svs --enable-demuxer=swf --enable-demuxer=tak --enable-demuxer=tedcaptions --enable-demuxer=thp --enable-demuxer=threedostr --enable-demuxer=tiertexseq --enable-demuxer=tmv --enable-demuxer=truehd --enable-demuxer=tta --enable-demuxer=tty --enable-demuxer=txd --enable-demuxer=ty --enable-demuxer=v210 --enable-demuxer=v210x --enable-demuxer=vag --enable-demuxer=vapoursynth --enable-demuxer=vc1 --enable-demuxer=vc1t --enable-demuxer=vividas --enable-demuxer=vivo --enable-demuxer=vmd --enable-demuxer=vobsub --enable-demuxer=voc --enable-demuxer=vpk --enable-demuxer=vplayer --enable-demuxer=vqf --enable-demuxer=w64 --enable-demuxer=wady --enable-demuxer=wav --enable-demuxer=wavarc --enable-demuxer=wc3 --enable-demuxer=webm_dash_manifest --enable-demuxer=webvtt --enable-demuxer=wsaud --enable-demuxer=wsd --enable-demuxer=wsvqa --enable-demuxer=wtv --enable-demuxer=wv --enable-demuxer=wve --enable-demuxer=xa --enable-demuxer=xbin --enable-demuxer=xmd --enable-demuxer=xmv --enable-demuxer=xvag --enable-demuxer=xwma --enable-demuxer=yop --enable-demuxer=yuv4mpegpipe --enable-encoder=a64multi --enable-encoder=a64multi5 --enable-encoder=aac --enable-encoder=ac3 --enable-encoder=ac3_fixed --enable-encoder=adpcm_adx --enable-encoder=adpcm_argo --enable-encoder=adpcm_g722 --enable-encoder=adpcm_g726 --enable-encoder=adpcm_g726le --enable-encoder=adpcm_ima_alp --enable-encoder=adpcm_ima_amv --enable-encoder=adpcm_ima_apm --enable-encoder=adpcm_ima_qt --enable-encoder=adpcm_ima_ssi --enable-encoder=adpcm_ima_wav --enable-encoder=adpcm_ima_ws --enable-encoder=adpcm_ms --enable-encoder=adpcm_swf --enable-encoder=adpcm_yamaha --enable-encoder=alac --enable-encoder=alias_pix --enable-encoder=amv --enable-encoder=anull --enable-encoder=apng --enable-encoder=aptx --enable-encoder=aptx_hd --enable-encoder=ass --enable-encoder=asv1 --enable-encoder=asv2 --enable-encoder=av1_nvenc --enable-encoder=avrp --enable-encoder=avui --enable-encoder=ayuv --enable-encoder=bitpacked --enable-encoder=bmp --enable-encoder=cfhd --enable-encoder=cinepak --enable-encoder=cljr --enable-encoder=comfortnoise --enable-encoder=dca --enable-encoder=dfpwm --enable-encoder=dnxhd --enable-encoder=dpx --enable-encoder=dvbsub --enable-encoder=dvdsub --enable-encoder=dvvideo --enable-encoder=eac3 --enable-encoder=exr --enable-encoder=ffv1 --enable-encoder=ffvhuff --enable-encoder=fits --enable-encoder=flac --enable-encoder=flashsv --enable-encoder=flashsv2 --enable-encoder=flv --enable-encoder=g723_1 --enable-encoder=gif --enable-encoder=h261 --enable-encoder=h263 --enable-encoder=h263_v4l2m2m --enable-encoder=h263p --enable-encoder=h264_nvenc --enable-encoder=h264_omx --enable-encoder=h264_v4l2m2m --enable-encoder=hap --enable-encoder=hdr --enable-encoder=hevc_nvenc --enable-encoder=hevc_v4l2m2m --enable-encoder=huffyuv --enable-encoder=jpeg2000 --enable-encoder=jpegls --enable-encoder=libaom_av1 --enable-encoder=libcodec2 --enable-encoder=libfdk_aac --enable-encoder=libgsm --enable-encoder=libgsm_ms --enable-encoder=libmp3lame --enable-encoder=libopencore_amrnb --enable-encoder=libopenh264 --enable-encoder=libopenjpeg --enable-encoder=libopus --enable-encoder=libshine --enable-encoder=libspeex --enable-encoder=libtheora --enable-encoder=libtwolame --enable-encoder=libvo_amrwbenc --enable-encoder=libvorbis --enable-encoder=libvpx_vp8 --enable-encoder=libvpx_vp9 --enable-encoder=libwebp --enable-encoder=libwebp_anim --enable-encoder=libx264 --enable-encoder=libx264rgb --enable-encoder=libx265 --enable-encoder=libxavs2 --enable-encoder=libxvid --enable-encoder=ljpeg --enable-encoder=magicyuv --enable-encoder=mjpeg --enable-encoder=mlp --enable-encoder=movtext --enable-encoder=mp2 --enable-encoder=mp2fixed --enable-encoder=mpeg1video --enable-encoder=mpeg2video --enable-encoder=mpeg4 --enable-encoder=mpeg4_omx --enable-encoder=mpeg4_v4l2m2m --enable-encoder=msmpeg4v2 --enable-encoder=msmpeg4v3 --enable-encoder=msvideo1 --enable-encoder=nellymoser --enable-encoder=opus --enable-encoder=pam --enable-encoder=pbm --enable-encoder=pcm_alaw --enable-encoder=pcm_bluray --enable-encoder=pcm_dvd --enable-encoder=pcm_f32be --enable-encoder=pcm_f32le --enable-encoder=pcm_f64be --enable-encoder=pcm_f64le --enable-encoder=pcm_mulaw --enable-encoder=pcm_s16be --enable-encoder=pcm_s16be_planar --enable-encoder=pcm_s16le --enable-encoder=pcm_s16le_planar --enable-encoder=pcm_s24be --enable-encoder=pcm_s24daud --enable-encoder=pcm_s24le --enable-encoder=pcm_s24le_planar --enable-encoder=pcm_s32be --enable-encoder=pcm_s32le --enable-encoder=pcm_s32le_planar --enable-encoder=pcm_s64be --enable-encoder=pcm_s64le --enable-encoder=pcm_s8 --enable-encoder=pcm_s8_planar --enable-encoder=pcm_u16be --enable-encoder=pcm_u16le --enable-encoder=pcm_u24be --enable-encoder=pcm_u24le --enable-encoder=pcm_u32be --enable-encoder=pcm_u32le --enable-encoder=pcm_u8 --enable-encoder=pcm_vidc --enable-encoder=pcx --enable-encoder=pfm --enable-encoder=pgm --enable-encoder=pgmyuv --enable-encoder=phm --enable-encoder=png --enable-encoder=ppm --enable-encoder=prores --enable-encoder=prores_aw --enable-encoder=prores_ks --enable-encoder=qoi --enable-encoder=qtrle --enable-encoder=r10k --enable-encoder=r210 --enable-encoder=ra_144 --enable-encoder=rawvideo --enable-encoder=roq --enable-encoder=roq_dpcm --enable-encoder=rpza --enable-encoder=rv10 --enable-encoder=rv20 --enable-encoder=s302m --enable-encoder=sbc --enable-encoder=sgi --enable-encoder=smc --enable-encoder=snow --enable-encoder=sonic --enable-encoder=sonic_ls --enable-encoder=speedhq --enable-encoder=srt --enable-encoder=ssa --enable-encoder=subrip --enable-encoder=sunrast --enable-encoder=svq1 --enable-encoder=targa --enable-encoder=text --enable-encoder=tiff --enable-encoder=truehd --enable-encoder=tta --enable-encoder=ttml --enable-encoder=utvideo --enable-encoder=v210 --enable-encoder=v308 --enable-encoder=v408 --enable-encoder=v410 --enable-encoder=vbn --enable-encoder=vc2 --enable-encoder=vnull --enable-encoder=vorbis --enable-encoder=vp8_v4l2m2m --enable-encoder=wavpack --enable-encoder=wbmp --enable-encoder=webvtt --enable-encoder=wmav1 --enable-encoder=wmav2 --enable-encoder=wmv1 --enable-encoder=wmv2 --enable-encoder=wrapped_avframe --enable-encoder=xbm --enable-encoder=xface --enable-encoder=xsub --enable-encoder=xwd --enable-encoder=y41p --enable-encoder=yuv4 --enable-encoder=zlib --enable-encoder=zmbv --enable-filter=a3dscope --enable-filter=abench --enable-filter=abitscope --enable-filter=acompressor --enable-filter=acontrast --enable-filter=acopy --enable-filter=acrossfade --enable-filter=acrossover --enable-filter=acrusher --enable-filter=acue --enable-filter=addroi --enable-filter=adeclick --enable-filter=adeclip --enable-filter=adecorrelate --enable-filter=adelay --enable-filter=adenorm --enable-filter=aderivative --enable-filter=adrawgraph --enable-filter=adrc --enable-filter=adynamicequalizer --enable-filter=adynamicsmooth --enable-filter=aecho --enable-filter=aemphasis --enable-filter=aeval --enable-filter=aevalsrc --enable-filter=aexciter --enable-filter=afade --enable-filter=afdelaysrc --enable-filter=afftdn --enable-filter=afftfilt --enable-filter=afifo --enable-filter=afir --enable-filter=afirsrc --enable-filter=aformat --enable-filter=afreqshift --enable-filter=afwtdn --enable-filter=agate --enable-filter=agraphmonitor --enable-filter=ahistogram --enable-filter=aiir --enable-filter=aintegral --enable-filter=ainterleave --enable-filter=alatency --enable-filter=alimiter --enable-filter=allpass --enable-filter=allrgb --enable-filter=allyuv --enable-filter=aloop --enable-filter=alphaextract --enable-filter=alphamerge --enable-filter=amerge --enable-filter=ametadata --enable-filter=amix --enable-filter=amovie --enable-filter=amplify --enable-filter=amultiply --enable-filter=anequalizer --enable-filter=anlmdn --enable-filter=anlmf --enable-filter=anlms --enable-filter=anoisesrc --enable-filter=anull --enable-filter=anullsink --enable-filter=anullsrc --enable-filter=apad --enable-filter=aperms --enable-filter=aphasemeter --enable-filter=aphaser --enable-filter=aphaseshift --enable-filter=apsyclip --enable-filter=apulsator --enable-filter=arealtime --enable-filter=aresample --enable-filter=areverse --enable-filter=arnndn --enable-filter=asdr --enable-filter=asegment --enable-filter=aselect --enable-filter=asendcmd --enable-filter=asetnsamples --enable-filter=asetpts --enable-filter=asetrate --enable-filter=asettb --enable-filter=ashowinfo --enable-filter=asidedata --enable-filter=asoftclip --enable-filter=aspectralstats --enable-filter=asplit --enable-filter=asr --enable-filter=ass --enable-filter=astats --enable-filter=astreamselect --enable-filter=asubboost --enable-filter=asubcut --enable-filter=asupercut --enable-filter=asuperpass --enable-filter=asuperstop --enable-filter=atadenoise --enable-filter=atempo --enable-filter=atilt --enable-filter=atrim --enable-filter=avectorscope --enable-filter=avgblur --enable-filter=avgblur_opencl --enable-filter=avsynctest --enable-filter=axcorrelate --enable-filter=azmq --enable-filter=backgroundkey --enable-filter=bandpass --enable-filter=bandreject --enable-filter=bass --enable-filter=bbox --enable-filter=bench --enable-filter=bilateral --enable-filter=bilateral_cuda --enable-filter=biquad --enable-filter=bitplanenoise --enable-filter=blackdetect --enable-filter=blackframe --enable-filter=blend --enable-filter=blockdetect --enable-filter=blurdetect --enable-filter=bm3d --enable-filter=boxblur --enable-filter=boxblur_opencl --enable-filter=bs2b --enable-filter=bwdif --enable-filter=cas --enable-filter=cellauto --enable-filter=channelmap --enable-filter=channelsplit --enable-filter=chorus --enable-filter=chromahold --enable-filter=chromakey --enable-filter=chromakey_cuda --enable-filter=chromanr --enable-filter=chromashift --enable-filter=ciescope --enable-filter=codecview --enable-filter=color --enable-filter=colorbalance --enable-filter=colorchannelmixer --enable-filter=colorchart --enable-filter=colorcontrast --enable-filter=colorcorrect --enable-filter=colorhold --enable-filter=colorize --enable-filter=colorkey --enable-filter=colorkey_opencl --enable-filter=colorlevels --enable-filter=colormap --enable-filter=colormatrix --enable-filter=colorspace --enable-filter=colorspace_cuda --enable-filter=colorspectrum --enable-filter=colortemperature --enable-filter=compand --enable-filter=compensationdelay --enable-filter=concat --enable-filter=convolution --enable-filter=convolution_opencl --enable-filter=convolve --enable-filter=copy --enable-filter=corr --enable-filter=cover_rect --enable-filter=crop --enable-filter=cropdetect --enable-filter=crossfeed --enable-filter=crystalizer --enable-filter=cue --enable-filter=curves --enable-filter=datascope --enable-filter=dblur --enable-filter=dcshift --enable-filter=dctdnoiz --enable-filter=deband --enable-filter=deblock --enable-filter=decimate --enable-filter=deconvolve --enable-filter=dedot --enable-filter=deesser --enable-filter=deflate --enable-filter=deflicker --enable-filter=dejudder --enable-filter=delogo --enable-filter=derain --enable-filter=deshake --enable-filter=deshake_opencl --enable-filter=despill --enable-filter=detelecine --enable-filter=dialoguenhance --enable-filter=dilation --enable-filter=dilation_opencl --enable-filter=displace --enable-filter=dnn_classify --enable-filter=dnn_detect --enable-filter=dnn_processing --enable-filter=doubleweave --enable-filter=drawbox --enable-filter=drawgraph --enable-filter=drawgrid --enable-filter=drawtext --enable-filter=drmeter --enable-filter=dynaudnorm --enable-filter=earwax --enable-filter=ebur128 --enable-filter=edgedetect --enable-filter=elbg --enable-filter=entropy --enable-filter=epx --enable-filter=eq --enable-filter=equalizer --enable-filter=erosion --enable-filter=erosion_opencl --enable-filter=estdif --enable-filter=exposure --enable-filter=extractplanes --enable-filter=extrastereo --enable-filter=fade --enable-filter=feedback --enable-filter=fftdnoiz --enable-filter=fftfilt --enable-filter=field --enable-filter=fieldhint --enable-filter=fieldmatch --enable-filter=fieldorder --enable-filter=fifo --enable-filter=fillborders --enable-filter=find_rect --enable-filter=firequalizer --enable-filter=flanger --enable-filter=flite --enable-filter=floodfill --enable-filter=format --enable-filter=fps --enable-filter=framepack --enable-filter=framerate --enable-filter=framestep --enable-filter=freezedetect --enable-filter=freezeframes --enable-filter=frei0r --enable-filter=frei0r_src --enable-filter=fspp --enable-filter=gblur --enable-filter=geq --enable-filter=gradfun --enable-filter=gradients --enable-filter=graphmonitor --enable-filter=grayworld --enable-filter=greyedge --enable-filter=guided --enable-filter=haas --enable-filter=haldclut --enable-filter=haldclutsrc --enable-filter=hdcd --enable-filter=headphone --enable-filter=hflip --enable-filter=highpass --enable-filter=highshelf --enable-filter=hilbert --enable-filter=histeq --enable-filter=histogram --enable-filter=hqdn3d --enable-filter=hqx --enable-filter=hstack --enable-filter=hsvhold --enable-filter=hsvkey --enable-filter=hue --enable-filter=huesaturation --enable-filter=hwdownload --enable-filter=hwmap --enable-filter=hwupload --enable-filter=hwupload_cuda --enable-filter=hysteresis --enable-filter=iccdetect --enable-filter=iccgen --enable-filter=identity --enable-filter=idet --enable-filter=il --enable-filter=inflate --enable-filter=interlace --enable-filter=interleave --enable-filter=join --enable-filter=kerndeint --enable-filter=kirsch --enable-filter=ladspa --enable-filter=lagfun --enable-filter=latency --enable-filter=lenscorrection --enable-filter=libplacebo --enable-filter=life --enable-filter=limitdiff --enable-filter=limiter --enable-filter=loop --enable-filter=loudnorm --enable-filter=lowpass --enable-filter=lowshelf --enable-filter=lumakey --enable-filter=lut --enable-filter=lut1d --enable-filter=lut2 --enable-filter=lut3d --enable-filter=lutrgb --enable-filter=lutyuv --enable-filter=lv2 --enable-filter=mandelbrot --enable-filter=maskedclamp --enable-filter=maskedmax --enable-filter=maskedmerge --enable-filter=maskedmin --enable-filter=maskedthreshold --enable-filter=maskfun --enable-filter=mcompand --enable-filter=median --enable-filter=mergeplanes --enable-filter=mestimate --enable-filter=metadata --enable-filter=midequalizer --enable-filter=minterpolate --enable-filter=mix --enable-filter=monochrome --enable-filter=morpho --enable-filter=movie --enable-filter=mpdecimate --enable-filter=mptestsrc --enable-filter=msad --enable-filter=multiply --enable-filter=negate --enable-filter=nlmeans --enable-filter=nlmeans_opencl --enable-filter=nnedi --enable-filter=noformat --enable-filter=noise --enable-filter=normalize --enable-filter=null --enable-filter=nullsink --enable-filter=nullsrc --enable-filter=ocr --enable-filter=ocv --enable-filter=openclsrc --enable-filter=oscilloscope --enable-filter=overlay --enable-filter=overlay_cuda --enable-filter=overlay_opencl --enable-filter=owdenoise --enable-filter=pad --enable-filter=pad_opencl --enable-filter=pal100bars --enable-filter=pal75bars --enable-filter=palettegen --enable-filter=paletteuse --enable-filter=pan --enable-filter=perms --enable-filter=perspective --enable-filter=phase --enable-filter=photosensitivity --enable-filter=pixdesctest --enable-filter=pixelize --enable-filter=pixscope --enable-filter=pp --enable-filter=pp7 --enable-filter=premultiply --enable-filter=prewitt --enable-filter=prewitt_opencl --enable-filter=program_opencl --enable-filter=pseudocolor --enable-filter=psnr --enable-filter=pullup --enable-filter=qp --enable-filter=random --enable-filter=readeia608 --enable-filter=readvitc --enable-filter=realtime --enable-filter=remap --enable-filter=remap_opencl --enable-filter=removegrain --enable-filter=removelogo --enable-filter=repeatfields --enable-filter=replaygain --enable-filter=reverse --enable-filter=rgbashift --enable-filter=rgbtestsrc --enable-filter=roberts --enable-filter=roberts_opencl --enable-filter=rotate --enable-filter=rubberband --enable-filter=sab --enable-filter=scale --enable-filter=scale_cuda --enable-filter=scale_npp --enable-filter=scale2ref --enable-filter=scale2ref_npp --enable-filter=scdet --enable-filter=scharr --enable-filter=scroll --enable-filter=segment --enable-filter=select --enable-filter=selectivecolor --enable-filter=sendcmd --enable-filter=separatefields --enable-filter=setdar --enable-filter=setfield --enable-filter=setparams --enable-filter=setpts --enable-filter=setrange --enable-filter=setsar --enable-filter=settb --enable-filter=sharpen_npp --enable-filter=shear --enable-filter=showcqt --enable-filter=showcwt --enable-filter=showfreqs --enable-filter=showinfo --enable-filter=showpalette --enable-filter=showspatial --enable-filter=showspectrum --enable-filter=showspectrumpic --enable-filter=showvolume --enable-filter=showwaves --enable-filter=showwavespic --enable-filter=shuffleframes --enable-filter=shufflepixels --enable-filter=shuffleplanes --enable-filter=sidechaincompress --enable-filter=sidechaingate --enable-filter=sidedata --enable-filter=sierpinski --enable-filter=signalstats --enable-filter=signature --enable-filter=silencedetect --enable-filter=silenceremove --enable-filter=sinc --enable-filter=sine --enable-filter=siti --enable-filter=smartblur --enable-filter=smptebars --enable-filter=smptehdbars --enable-filter=sobel --enable-filter=sobel_opencl --enable-filter=sofalizer --enable-filter=spectrumsynth --enable-filter=speechnorm --enable-filter=split --enable-filter=spp --enable-filter=sr --enable-filter=ssim --enable-filter=ssim360 --enable-filter=stereo3d --enable-filter=stereotools --enable-filter=stereowiden --enable-filter=streamselect --enable-filter=subtitles --enable-filter=super2xsai --enable-filter=superequalizer --enable-filter=surround --enable-filter=swaprect --enable-filter=swapuv --enable-filter=tblend --enable-filter=telecine --enable-filter=testsrc --enable-filter=testsrc2 --enable-filter=thistogram --enable-filter=threshold --enable-filter=thumbnail --enable-filter=thumbnail_cuda --enable-filter=tile --enable-filter=tiltshelf --enable-filter=tinterlace --enable-filter=tlut2 --enable-filter=tmedian --enable-filter=tmidequalizer --enable-filter=tmix --enable-filter=tonemap --enable-filter=tonemap_opencl --enable-filter=tpad --enable-filter=transpose --enable-filter=transpose_npp --enable-filter=transpose_opencl --enable-filter=treble --enable-filter=tremolo --enable-filter=trim --enable-filter=unpremultiply --enable-filter=unsharp --enable-filter=unsharp_opencl --enable-filter=untile --enable-filter=v360 --enable-filter=vaguedenoiser --enable-filter=varblur --enable-filter=vectorscope --enable-filter=vflip --enable-filter=vfrdet --enable-filter=vibrance --enable-filter=vibrato --enable-filter=vidstabdetect --enable-filter=vidstabtransform --enable-filter=vif --enable-filter=vignette --enable-filter=virtualbass --enable-filter=vmafmotion --enable-filter=volume --enable-filter=volumedetect --enable-filter=vstack --enable-filter=w3fdif --enable-filter=waveform --enable-filter=weave --enable-filter=xbr --enable-filter=xcorrelate --enable-filter=xfade --enable-filter=xfade_opencl --enable-filter=xmedian --enable-filter=xstack --enable-filter=yadif --enable-filter=yadif_cuda --enable-filter=yaepblur --enable-filter=yuvtestsrc --enable-filter=zmq --enable-filter=zoompan --enable-filter=zscale --enable-frei0r --enable-gcrypt --enable-gmp --enable-gnutls --enable-hwaccel=av1_nvdec --enable-hwaccel=h264_nvdec --enable-hwaccel=hevc_nvdec --enable-hwaccel=mjpeg_nvdec --enable-hwaccel=mpeg1_nvdec --enable-hwaccel=mpeg2_nvdec --enable-hwaccel=mpeg4_nvdec --enable-hwaccel=vc1_nvdec --enable-hwaccel=vp8_nvdec --enable-hwaccel=vp9_nvdec --enable-hwaccel=wmv3_nvdec --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libcodec2 --enable-libdav1d --enable-libdc1394 --enable-libdrm --enable-libfdk-aac --enable-libflite --enable-libfontconfig --enable-libfreetype --enable-libfribidi --enable-libgme --enable-libgsm --enable-libiec61883 --enable-libjack --enable-libmodplug --enable-libmp3lame --enable-libmysofa --enable-libnpp --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenh264 --enable-libopenjpeg --enable-libopenmpt --enable-libopus --enable-libplacebo --enable-libpulse --enable-librabbitmq --enable-librsvg --enable-librtmp --enable-librubberband --enable-libshine --enable-libsmbclient --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libsrt --enable-libssh --enable-libtesseract --enable-libtheora --enable-libtwolame --enable-libvidstab --enable-libvo-amrwbenc --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxavs2 --enable-libxcb --enable-libxcb-shape --enable-libxcb-shm --enable-libxcb-xfixes --enable-libxml2 --enable-libxvid --enable-libzimg --enable-libzmq --enable-libzvbi --enable-lv2 --enable-muxer=a64 --enable-muxer=ac3 --enable-muxer=adts --enable-muxer=adx --enable-muxer=aiff --enable-muxer=alp --enable-muxer=amr --enable-muxer=amv --enable-muxer=apm --enable-muxer=apng --enable-muxer=aptx --enable-muxer=aptx_hd --enable-muxer=argo_asf --enable-muxer=argo_cvg --enable-muxer=asf --enable-muxer=asf_stream --enable-muxer=ass --enable-muxer=ast --enable-muxer=au --enable-muxer=avi --enable-muxer=avif --enable-muxer=avm2 --enable-muxer=avs2 --enable-muxer=avs3 --enable-muxer=bit --enable-muxer=caf --enable-muxer=cavsvideo --enable-muxer=chromaprint --enable-muxer=codec2 --enable-muxer=codec2raw --enable-muxer=crc --enable-muxer=dash --enable-muxer=data --enable-muxer=daud --enable-muxer=dfpwm --enable-muxer=dirac --enable-muxer=dnxhd --enable-muxer=dts --enable-muxer=dv --enable-muxer=eac3 --enable-muxer=f4v --enable-muxer=ffmetadata --enable-muxer=fifo --enable-muxer=fifo_test --enable-muxer=filmstrip --enable-muxer=fits --enable-muxer=flac --enable-muxer=flv --enable-muxer=framecrc --enable-muxer=framehash --enable-muxer=framemd5 --enable-muxer=g722 --enable-muxer=g723_1 --enable-muxer=g726 --enable-muxer=g726le --enable-muxer=gif --enable-muxer=gsm --enable-muxer=gxf --enable-muxer=h261 --enable-muxer=h263 --enable-muxer=h264 --enable-muxer=hash --enable-muxer=hds --enable-muxer=hevc --enable-muxer=hls --enable-muxer=ico --enable-muxer=ilbc --enable-muxer=image2 --enable-muxer=image2pipe --enable-muxer=ipod --enable-muxer=ircam --enable-muxer=ismv --enable-muxer=ivf --enable-muxer=jacosub --enable-muxer=kvag --enable-muxer=latm --enable-muxer=lrc --enable-muxer=m4v --enable-muxer=matroska --enable-muxer=matroska_audio --enable-muxer=md5 --enable-muxer=microdvd --enable-muxer=mjpeg --enable-muxer=mkvtimestamp_v2 --enable-muxer=mlp --enable-muxer=mmf --enable-muxer=mov --enable-muxer=mp2 --enable-muxer=mp3 --enable-muxer=mp4 --enable-muxer=mpeg1system --enable-muxer=mpeg1vcd --enable-muxer=mpeg1video --enable-muxer=mpeg2dvd --enable-muxer=mpeg2svcd --enable-muxer=mpeg2video --enable-muxer=mpeg2vob --enable-muxer=mpegts --enable-muxer=mpjpeg --enable-muxer=mxf --enable-muxer=mxf_d10 --enable-muxer=mxf_opatom --enable-muxer=null --enable-muxer=nut --enable-muxer=obu --enable-muxer=oga --enable-muxer=ogg --enable-muxer=ogv --enable-muxer=oma --enable-muxer=opus --enable-muxer=pcm_alaw --enable-muxer=pcm_f32be --enable-muxer=pcm_f32le --enable-muxer=pcm_f64be --enable-muxer=pcm_f64le --enable-muxer=pcm_mulaw --enable-muxer=pcm_s16be --enable-muxer=pcm_s16le --enable-muxer=pcm_s24be --enable-muxer=pcm_s24le --enable-muxer=pcm_s32be --enable-muxer=pcm_s32le --enable-muxer=pcm_s8 --enable-muxer=pcm_u16be --enable-muxer=pcm_u16le --enable-muxer=pcm_u24be --enable-muxer=pcm_u24le --enable-muxer=pcm_u32be --enable-muxer=pcm_u32le --enable-muxer=pcm_u8 --enable-muxer=pcm_vidc --enable-muxer=psp --enable-muxer=rawvideo --enable-muxer=rm --enable-muxer=roq --enable-muxer=rso --enable-muxer=rtp --enable-muxer=rtp_mpegts --enable-muxer=rtsp --enable-muxer=sap --enable-muxer=sbc --enable-muxer=scc --enable-muxer=segafilm --enable-muxer=segment --enable-muxer=smjpeg --enable-muxer=smoothstreaming --enable-muxer=sox --enable-muxer=spdif --enable-muxer=spx --enable-muxer=srt --enable-muxer=stream_segment --enable-muxer=streamhash --enable-muxer=sup --enable-muxer=swf --enable-muxer=tee --enable-muxer=tg2 --enable-muxer=tgp --enable-muxer=truehd --enable-muxer=tta --enable-muxer=ttml --enable-muxer=uncodedframecrc --enable-muxer=vc1 --enable-muxer=vc1t --enable-muxer=voc --enable-muxer=w64 --enable-muxer=wav --enable-muxer=webm --enable-muxer=webm_chunk --enable-muxer=webm_dash_manifest --enable-muxer=webp --enable-muxer=webvtt --enable-muxer=wsaud --enable-muxer=wtv --enable-muxer=wv --enable-muxer=yuv4mpegpipe --enable-nvdec --enable-nvenc --enable-omx --enable-openal --enable-opencl --enable-opengl --enable-parser=aac --enable-parser=aac_latm --enable-parser=ac3 --enable-parser=adx --enable-parser=amr --enable-parser=av1 --enable-parser=avs2 --enable-parser=avs3 --enable-parser=bmp --enable-parser=cavsvideo --enable-parser=cook --enable-parser=cri --enable-parser=dca --enable-parser=dirac --enable-parser=dnxhd --enable-parser=dolby_e --enable-parser=dpx --enable-parser=dvaudio --enable-parser=dvbsub --enable-parser=dvd_nav --enable-parser=dvdsub --enable-parser=flac --enable-parser=ftr --enable-parser=g723_1 --enable-parser=g729 --enable-parser=gif --enable-parser=gsm --enable-parser=h261 --enable-parser=h263 --enable-parser=h264 --enable-parser=hdr --enable-parser=hevc --enable-parser=ipu --enable-parser=jpeg2000 --enable-parser=misc4 --enable-parser=mjpeg --enable-parser=mlp --enable-parser=mpeg4video --enable-parser=mpegaudio --enable-parser=mpegvideo --enable-parser=opus --enable-parser=png --enable-parser=pnm --enable-parser=qoi --enable-parser=rv30 --enable-parser=rv40 --enable-parser=sbc --enable-parser=sipr --enable-parser=tak --enable-parser=vc1 --enable-parser=vorbis --enable-parser=vp3 --enable-parser=vp8 --enable-parser=vp9 --enable-parser=webp --enable-parser=xbm --enable-parser=xma --enable-parser=xwd --enable-pocketsphinx --enable-protocol=async --enable-protocol=bluray --enable-protocol=cache --enable-protocol=concat --enable-protocol=concatf --enable-protocol=crypto --enable-protocol=data --enable-protocol=fd --enable-protocol=ffrtmpcrypt --enable-protocol=ffrtmphttp --enable-protocol=file --enable-protocol=ftp --enable-protocol=gopher --enable-protocol=gophers --enable-protocol=hls --enable-protocol=http --enable-protocol=httpproxy --enable-protocol=https --enable-protocol=icecast --enable-protocol=ipfs_gateway --enable-protocol=ipns_gateway --enable-protocol=libamqp --enable-protocol=librtmp --enable-protocol=librtmpe --enable-protocol=librtmps --enable-protocol=librtmpt --enable-protocol=librtmpte --enable-protocol=libsmbclient --enable-protocol=libsrt --enable-protocol=libssh --enable-protocol=libzmq --enable-protocol=md5 --enable-protocol=mmsh --enable-protocol=mmst --enable-protocol=pipe --enable-protocol=prompeg --enable-protocol=rtmp --enable-protocol=rtmpe --enable-protocol=rtmps --enable-protocol=rtmpt --enable-protocol=rtmpte --enable-protocol=rtmpts --enable-protocol=rtp --enable-protocol=sctp --enable-protocol=srtp --enable-protocol=subfile --enable-protocol=tcp --enable-protocol=tee --enable-protocol=tls --enable-protocol=udp --enable-protocol=udplite --enable-protocol=unix --enable-vapoursynth --enable-libaribb24"

ARG FFMPEG_EXTRA_FLAGS

RUN cd /opt/src/ffmpeg && \
	set -e && \
	PKG_CONFIG_PATH="/usr/local/lib/pkgconfig" \
	./configure ${FFMPEG_CONFIG_FLAGS} \
	${FFMPEG_EXTRA_FLAGS} \
	--enable-nonfree \
	--enable-version3 \
	--enable-gpl \
	--disable-shared \
	--enable-static \
	--disable-doc \
	--disable-ffplay \
	--disable-stripping \
	--extra-cflags=-I/usr/local/cuda/include \
	--extra-cflags=-I../nv-codec-headers/include \
	--extra-ldflags=-L/usr/local/cuda/lib64 || \
	( echo "Configure failed, here's ffbuild/config.log:" ; cat ffbuild/config.log ; exit 1 ) && \
	make -j$(nproc)

FROM nvidia/cuda:${RUNIMAGE}
ARG CUDA
ARG OS

RUN echo "/usr/lib/x86_64-linux-gnu" | tee -a /etc/ld.so.conf && \
	ldconfig && \
	mkdir -p /etc/ffmpeg && \
	addgroup --gid 1000 ffmpeg && \
	adduser --home /etc/ffmpeg \
	--shell /usr/sbin/nologin \
	--no-create-home \
	--uid 1000 \
	--gid 1000 \
	--gecos ffmpeg,ffmpeg,,,ffmpeg \
	--disabled-password \
	--disabled-login \
	ffmpeg && \
	chown -R ffmpeg:ffmpeg /etc/ffmpeg && \
	apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -qqy install \
	fontconfig \
	fonts-lato \
	fonts-open-sans \
	fonts-roboto \
	fonts-mononoki \
	liblensfun-data-v1 \
	python3 \
	python3-pip \
	sudo \
	tzdata && \
	rm -rf /var/lib/apt/lists/* && \
	fc-cache -v /usr/share/fonts && \
	pip install requests streamlink

COPY --from=builder /opt/src/ffmpeg/ffmpeg \
	/opt/src/ffmpeg/ffprobe \
	/usr/bin/

COPY --from=builder \
	/opt/src/ffmpeg/libavcodec/libavcodec.so.* \
	/opt/src/ffmpeg/libavdevice/libavdevice.so.* \
	/opt/src/ffmpeg/libavfilter/libavfilter.so.* \
	/opt/src/ffmpeg/libavformat/libavformat.so.* \
	/opt/src/ffmpeg/libavutil/libavutil.so.* \
	/opt/src/ffmpeg/libpostproc/libpostproc.so.* \
	/opt/src/ffmpeg/libswresample/libswresample.so.* \
	/opt/src/ffmpeg/libswscale/libswscale.so.* \
	/usr/lib/libvapoursynth-script.so.* \
	/usr/lib/x86_64-linux-gnu/libvulkan.so.* \
	/usr/lib/x86_64-linux-gnu/libepoxy.so.* \
	/usr/lib/x86_64-linux-gnu/libndr.so.* \
	/usr/lib/x86_64-linux-gnu/libsmbconf.so.* \
	/usr/lib/x86_64-linux-gnu/libsamba-util.so.* \
	/usr/lib/x86_64-linux-gnu/libsamba-errors.so.* \
	/usr/lib/x86_64-linux-gnu/libtevent-util.so.* \
	/usr/lib/x86_64-linux-gnu/libsamba-credentials.so.* \
	/usr/lib/x86_64-linux-gnu/libndr-standard.so.* \
	/usr/lib/x86_64-linux-gnu/libsamba-hostconfig.so.* \
	/usr/lib/x86_64-linux-gnu/libtevent.so.* \
	/usr/lib/x86_64-linux-gnu/libtalloc.so.* \
	/usr/lib/x86_64-linux-gnu/libdcerpc-binding.so.* \
	/usr/lib/x86_64-linux-gnu/libtdb.so.* \
	/usr/lib/x86_64-linux-gnu/libicui18n.so.* \
	/usr/lib/x86_64-linux-gnu/libldb.so.* \
	/usr/lib/x86_64-linux-gnu/libndr-nbt.so.* \
	/usr/lib/x86_64-linux-gnu/libwbclient.so.* \
	/usr/lib/x86_64-linux-gnu/libsamdb.so.* \
	/usr/lib/x86_64-linux-gnu/libndr-krb5pac.so.* \
	/usr/lib/x86_64-linux-gnu/libjansson.so.* \
	/usr/lib/x86_64-linux-gnu/samba \
	/usr/lib/x86_64-linux-gnu/blas \
	/usr/lib/x86_64-linux-gnu/lapack \
	/usr/lib/x86_64-linux-gnu/libaom.so.* \
	/usr/lib/x86_64-linux-gnu/libapparmor.so.* \
	/usr/lib/x86_64-linux-gnu/libarchive.so.* \
	/usr/lib/x86_64-linux-gnu/libasound.so.* \
	/usr/lib/x86_64-linux-gnu/libass.so.* \
	/usr/lib/x86_64-linux-gnu/libasyncns.so.* \
	/usr/lib/x86_64-linux-gnu/libavc1394.so.* \
	/usr/lib/x86_64-linux-gnu/libavcodec.so.* \
	/usr/lib/x86_64-linux-gnu/libavutil.so.* \
	/usr/lib/x86_64-linux-gnu/libblkid.so.* \
	/usr/lib/x86_64-linux-gnu/libbluray.so.* \
	/usr/lib/x86_64-linux-gnu/libbrotlicommon.so.* \
	/usr/lib/x86_64-linux-gnu/libbrotlidec.so.* \
	/usr/lib/x86_64-linux-gnu/libbs2b.so.* \
	/usr/lib/x86_64-linux-gnu/libbsd.so.* \
	/usr/lib/x86_64-linux-gnu/libc.so.* \
	/usr/lib/x86_64-linux-gnu/libcaca.so.* \
	/usr/lib/x86_64-linux-gnu/libcairo-gobject.so.* \
	/usr/lib/x86_64-linux-gnu/libcairo.so.* \
	/usr/lib/x86_64-linux-gnu/libcap.so.* \
	/usr/lib/x86_64-linux-gnu/libcdio_cdda.so.* \
	/usr/lib/x86_64-linux-gnu/libcdio_paranoia.so.* \
	/usr/lib/x86_64-linux-gnu/libcdio.so.* \
	/usr/lib/x86_64-linux-gnu/libchromaprint.so.* \
	/usr/lib/x86_64-linux-gnu/libcodec2.so.1.0 \
	/usr/lib/x86_64-linux-gnu/libcom_err.so.* \
	/usr/lib/x86_64-linux-gnu/libcrypto.so.* \
	/usr/lib/x86_64-linux-gnu/libdatrie.so.* \
	/usr/lib/x86_64-linux-gnu/libdav1d.so.* \
	/usr/lib/x86_64-linux-gnu/libdb-5.3.so \
	/usr/lib/x86_64-linux-gnu/libdbus-1.so.* \
	/usr/lib/x86_64-linux-gnu/libdc1394.so.* \
	/usr/lib/x86_64-linux-gnu/libdecor-0.so.* \
	/usr/lib/x86_64-linux-gnu/libdl.so.* \
	/usr/lib/x86_64-linux-gnu/libdrm.so.* \
	/usr/lib/x86_64-linux-gnu/libexpat.so.* \
	/usr/lib/x86_64-linux-gnu/libfdk-aac.so.* \
	/usr/lib/x86_64-linux-gnu/libffi.so.* \
	/usr/lib/x86_64-linux-gnu/libFLAC.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmu_us_awb.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmu_us_kal.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmu_us_kal16.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmu_us_rms.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmu_us_slt.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_cmulex.so.* \
	/usr/lib/x86_64-linux-gnu/libflite_usenglish.so.* \
	/usr/lib/x86_64-linux-gnu/libflite.so.* \
	/usr/lib/x86_64-linux-gnu/libfontconfig.so.* \
	/usr/lib/x86_64-linux-gnu/libfreetype.so.* \
	/usr/lib/x86_64-linux-gnu/libfribidi.so.* \
	/usr/lib/x86_64-linux-gnu/libgbm.so.* \
	/usr/lib/x86_64-linux-gnu/libgcc_s.so.* \
	/usr/lib/x86_64-linux-gnu/libgcrypt.so.* \
	/usr/lib/x86_64-linux-gnu/libgdk_pixbuf-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libgfortran.so.* \
	/usr/lib/x86_64-linux-gnu/libgif.so.* \
	/usr/lib/x86_64-linux-gnu/libgio-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libGL.so.* \
	/usr/lib/x86_64-linux-gnu/libGLdispatch.so.* \
	/usr/lib/x86_64-linux-gnu/libglib-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libGLX.so.* \
	/usr/lib/x86_64-linux-gnu/libgme.so.* \
	/usr/lib/x86_64-linux-gnu/libgmodule-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libgmp.so.* \
	/usr/lib/x86_64-linux-gnu/libgnutls.so.* \
	/usr/lib/x86_64-linux-gnu/libgobject-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libgomp.so.* \
	/usr/lib/x86_64-linux-gnu/libgraphite2.so.* \
	/usr/lib/x86_64-linux-gnu/libgsm.so.* \
	/usr/lib/x86_64-linux-gnu/libgssapi_krb5.so.* \
	/usr/lib/x86_64-linux-gnu/libharfbuzz.so.* \
	/usr/lib/x86_64-linux-gnu/libhogweed.so.* \
	/usr/lib/x86_64-linux-gnu/libicudata.so.* \
	/usr/lib/x86_64-linux-gnu/libicuuc.so.* \
	/usr/lib/x86_64-linux-gnu/libidn2.so.* \
	/usr/lib/x86_64-linux-gnu/libiec61883.so.* \
	/usr/lib/x86_64-linux-gnu/libjack.so.* \
	/usr/lib/x86_64-linux-gnu/libjpeg.so.* \
	/usr/lib/x86_64-linux-gnu/libk5crypto.so.* \
	/usr/lib/x86_64-linux-gnu/libkeyutils.so.* \
	/usr/lib/x86_64-linux-gnu/libkrb5.so.* \
	/usr/lib/x86_64-linux-gnu/libkrb5support.so.* \
	/usr/lib/x86_64-linux-gnu/liblcms2.so.* \
	/usr/lib/x86_64-linux-gnu/liblept.so.* \
	/usr/lib/x86_64-linux-gnu/liblilv-0.so.* \
	/usr/lib/x86_64-linux-gnu/liblz4.so.* \
	/usr/lib/x86_64-linux-gnu/liblzma.so.* \
	/usr/lib/x86_64-linux-gnu/libm.so.* \
	/usr/lib/x86_64-linux-gnu/libmd.so.* \
	/usr/lib/x86_64-linux-gnu/libmfx.so.* \
	/usr/lib/x86_64-linux-gnu/libmount.so.* \
	/usr/lib/x86_64-linux-gnu/libmp3lame.so.* \
	/usr/lib/x86_64-linux-gnu/libmpg123.so.* \
	/usr/lib/x86_64-linux-gnu/libmysofa.so.* \
	/usr/lib/x86_64-linux-gnu/libncursesw.so.* \
	/usr/lib/x86_64-linux-gnu/libnettle.so.* \
	/usr/lib/x86_64-linux-gnu/libnorm.so.* \
	/usr/lib/x86_64-linux-gnu/libnuma.so.* \
	/usr/lib/x86_64-linux-gnu/libogg.so.* \
	/usr/lib/x86_64-linux-gnu/libopenal.so.* \
	/usr/lib/x86_64-linux-gnu/libopencore-amrnb.so.* \
	/usr/lib/x86_64-linux-gnu/libopencore-amrwb.so.* \
	/usr/lib/x86_64-linux-gnu/libopenh264.so.* \
	/usr/lib/x86_64-linux-gnu/libopenjp2.so.* \
	/usr/lib/x86_64-linux-gnu/libopenmpt_modplug.so.* \
	/usr/lib/x86_64-linux-gnu/libopenmpt.so.* \
	/usr/lib/x86_64-linux-gnu/libopus.so.* \
	/usr/lib/x86_64-linux-gnu/libp11-kit.so.* \
	/usr/lib/x86_64-linux-gnu/libpango-1.0.so.* \
	/usr/lib/x86_64-linux-gnu/libpangocairo-1.0.so.* \
	/usr/lib/x86_64-linux-gnu/libpangoft2-1.0.so.* \
	/usr/lib/x86_64-linux-gnu/libpcre.so.* \
	/usr/lib/x86_64-linux-gnu/libpcre2-8.so.* \
	/usr/lib/x86_64-linux-gnu/libpgm-5.3.so.* \
	/usr/lib/x86_64-linux-gnu/libpixman-1.so.* \
	/usr/lib/x86_64-linux-gnu/libplacebo.so.* \
	/usr/lib/x86_64-linux-gnu/libpng16.so.* \
	/usr/lib/x86_64-linux-gnu/libpocketsphinx.so.* \
	/usr/lib/x86_64-linux-gnu/libpthread.so.* \
	/usr/lib/x86_64-linux-gnu/libpulse.so.* \
	/usr/lib/x86_64-linux-gnu/libquadmath.so.* \
	/usr/lib/x86_64-linux-gnu/librabbitmq.so.* \
	/usr/lib/x86_64-linux-gnu/libraw1394.so.* \
	/usr/lib/x86_64-linux-gnu/libresolv.so.* \
	/usr/lib/x86_64-linux-gnu/librom1394.so.* \
	/usr/lib/x86_64-linux-gnu/librsvg-2.so.* \
	/usr/lib/x86_64-linux-gnu/librt.so.* \
	/usr/lib/x86_64-linux-gnu/librtmp.so.* \
	/usr/lib/x86_64-linux-gnu/librubberband.so.* \
	/usr/lib/x86_64-linux-gnu/libsamplerate.so.* \
	/usr/lib/x86_64-linux-gnu/libSDL2-2.0.so.* \
	/usr/lib/x86_64-linux-gnu/libselinux.so.* \
	/usr/lib/x86_64-linux-gnu/libserd-0.so.* \
	/usr/lib/x86_64-linux-gnu/libshine.so.* \
	/usr/lib/x86_64-linux-gnu/libslang.so.* \
	/usr/lib/x86_64-linux-gnu/libsmbclient.so.* \
	/usr/lib/x86_64-linux-gnu/libsnappy.so.* \
	/usr/lib/x86_64-linux-gnu/libsndfile.so.* \
	/usr/lib/x86_64-linux-gnu/libsndio.so.* \
	/usr/lib/x86_64-linux-gnu/libsodium.so.* \
	/usr/lib/x86_64-linux-gnu/libsord-0.so.* \
	/usr/lib/x86_64-linux-gnu/libsoxr.so.* \
	/usr/lib/x86_64-linux-gnu/libspeex.so.* \
	/usr/lib/x86_64-linux-gnu/libsphinxbase.so.* \
	/usr/lib/x86_64-linux-gnu/libsratom-0.so.* \
	/usr/lib/x86_64-linux-gnu/libsrt-gnutls.so.1.4 \
	/usr/lib/x86_64-linux-gnu/libssh.so.* \
	/usr/lib/x86_64-linux-gnu/libssl.so.* \
	/usr/lib/x86_64-linux-gnu/libstdc++.so.* \
	/usr/lib/x86_64-linux-gnu/libswresample.so.* \
	/usr/lib/x86_64-linux-gnu/libsystemd.so.* \
	/usr/lib/x86_64-linux-gnu/libtasn1.so.* \
	/usr/lib/x86_64-linux-gnu/libtesseract.so.* \
	/usr/lib/x86_64-linux-gnu/libthai.so.* \
	/usr/lib/x86_64-linux-gnu/libtheoradec.so.* \
	/usr/lib/x86_64-linux-gnu/libtheoraenc.so.* \
	/usr/lib/x86_64-linux-gnu/libtinfo.so.* \
	/usr/lib/x86_64-linux-gnu/libtwolame.so.* \
	/usr/lib/x86_64-linux-gnu/libudev.so.* \
	/usr/lib/x86_64-linux-gnu/libudfread.so.* \
	/usr/lib/x86_64-linux-gnu/libunistring.so.* \
	/usr/lib/x86_64-linux-gnu/libusb-1.0.so.* \
	/usr/lib/x86_64-linux-gnu/libuuid.so.* \
	/usr/lib/x86_64-linux-gnu/libva-drm.so.* \
	/usr/lib/x86_64-linux-gnu/libva-x11.so.* \
	/usr/lib/x86_64-linux-gnu/libva.so.* \
	/usr/lib/x86_64-linux-gnu/libvapoursynth-script.so.* \
	/usr/lib/x86_64-linux-gnu/libvdpau.so.* \
	/usr/lib/x86_64-linux-gnu/libvidstab.so.1.1 \
	/usr/lib/x86_64-linux-gnu/libvo-amrwbenc.so.* \
	/usr/lib/x86_64-linux-gnu/libvorbis.so.* \
	/usr/lib/x86_64-linux-gnu/libvorbisenc.so.* \
	/usr/lib/x86_64-linux-gnu/libvorbisfile.so.* \
	/usr/lib/x86_64-linux-gnu/libvpx.so.* \
	/usr/lib/x86_64-linux-gnu/libwayland-client.so.* \
	/usr/lib/x86_64-linux-gnu/libwayland-cursor.so.* \
	/usr/lib/x86_64-linux-gnu/libwayland-egl.so.* \
	/usr/lib/x86_64-linux-gnu/libwayland-server.so.* \
	/usr/lib/x86_64-linux-gnu/libwebp.so.* \
	/usr/lib/x86_64-linux-gnu/libwebpmux.so.* \
	/usr/lib/x86_64-linux-gnu/libX11-xcb.so.* \
	/usr/lib/x86_64-linux-gnu/libX11.so.* \
	/usr/lib/x86_64-linux-gnu/libx264.so.* \
	/usr/lib/x86_64-linux-gnu/libx265.so.* \
	/usr/lib/x86_64-linux-gnu/libXau.so.* \
	/usr/lib/x86_64-linux-gnu/libxavs2.so.* \
	/usr/lib/x86_64-linux-gnu/libxcb-render.so.* \
	/usr/lib/x86_64-linux-gnu/libxcb-shape.so.* \
	/usr/lib/x86_64-linux-gnu/libxcb-shm.so.* \
	/usr/lib/x86_64-linux-gnu/libxcb-xfixes.so.* \
	/usr/lib/x86_64-linux-gnu/libxcb.so.* \
	/usr/lib/x86_64-linux-gnu/libXcursor.so.* \
	/usr/lib/x86_64-linux-gnu/libXdmcp.so.* \
	/usr/lib/x86_64-linux-gnu/libXext.so.* \
	/usr/lib/x86_64-linux-gnu/libXfixes.so.* \
	/usr/lib/x86_64-linux-gnu/libXi.so.* \
	/usr/lib/x86_64-linux-gnu/libXinerama.so.* \
	/usr/lib/x86_64-linux-gnu/libxkbcommon.so.* \
	/usr/lib/x86_64-linux-gnu/libxml2.so.* \
	/usr/lib/x86_64-linux-gnu/libXrandr.so.* \
	/usr/lib/x86_64-linux-gnu/libXrender.so.* \
	/usr/lib/x86_64-linux-gnu/libXss.so.* \
	/usr/lib/x86_64-linux-gnu/libXv.so.* \
	/usr/lib/x86_64-linux-gnu/libxvidcore.so.* \
	/usr/lib/x86_64-linux-gnu/libXxf86vm.so.* \
	/usr/lib/x86_64-linux-gnu/libz.so.* \
	/usr/lib/x86_64-linux-gnu/libzimg.so.* \
	/usr/lib/x86_64-linux-gnu/libzmq.so.* \
	/usr/lib/x86_64-linux-gnu/libzstd.so.* \
	/usr/lib/x86_64-linux-gnu/libzvbi.so.* \
	/usr/lib/x86_64-linux-gnu/libaribb24.so.* \
	/lib/x86_64-linux-gnu/

COPY --from=builder \
	/usr/lib/x86_64-linux-gnu/pulseaudio/libpulsecommon-15.99.so \
	/usr/lib/x86_64-linux-gnu/pulseaudio/

COPY ./ffmpeg/rootfs /

ENV UID=1000
ENV GID=1000

RUN /usr/bin/ffmpeg -version

ARG BUILD_DESCRIPTION="NVIDIA® CUDA® Toolkit"
ARG BUILD_NAME="NVIDIA® CUDA® Toolkit"
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

LABEL \
	maintainer="Troy Kelly <troy@aperim.com>" \
	org.opencontainers.image.title="${BUILD_NAME} ${CUDA}" \
	org.opencontainers.image.description="${BUILD_DESCRIPTION} ${CUDA} for ${OS} with ffmpeg ${FFMPEG_VERSION}" \
	org.opencontainers.image.vendor="Troy Kelly" \
	org.opencontainers.image.authors="Troy Kelly <troy@aperim.com>" \
	org.opencontainers.image.licenses="Apache-2.0" \
	org.opencontainers.image.url="https://aperim.com" \
	org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
	org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
	org.opencontainers.image.created=${BUILD_DATE} \
	org.opencontainers.image.revision=${BUILD_REF} \
	org.opencontainers.image.version=${BUILD_VERSION}

ENTRYPOINT ["docker-ffmpeg-entrypoint"]