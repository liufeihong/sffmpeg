PROJECT(sffmpeg)

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
INCLUDE(ExternalProject)
INCLUDE_DIRECTORIES(BEFORE ${CMAKE_BINARY_DIR}/include)
LINK_DIRECTORIES(${CMAKE_BINARY_DIR}/lib)
SET(ENV{PATH} ${CMAKE_BINARY_DIR}/bin:$ENV{PATH})
SET(ENV{PKG_CONFIG_PATH} ${CMAKE_BINARY_DIR}/lib/pkgconfig)

EXTERNALPROJECT_ADD(
  yasm
  # URL http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/yasm-1.3.0.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh yasm
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  zlib
  # URL https://github.com/madler/zlib/archive/v1.2.11.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/zlib-v1.2.11.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh zlib
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  openssl
  # URL http://www.openssl.org/source/openssl-1.0.2-latest.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/openssl-1.0.2-latest.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh openssl
  CONFIGURE_COMMAND ./config no-shared no-idea no-mdc2 no-rc5 --prefix=${CMAKE_BINARY_DIR}
  BUILD_COMMAND make depend && make
  INSTALL_COMMAND make install_sw
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  opencore-amr
  # URL http://downloads.sourceforge.net/project/opencore-amr/opencore-amr/opencore-amr-0.1.5.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/opencore-amr-0.1.5.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh opencore-amr
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fdkaac
  # URL http://downloads.sourceforge.net/project/opencore-amr/fdk-aac/fdk-aac-0.1.5.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/fdk-aac-0.1.5.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh fdkaac
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  lame
  # URL http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/lame-3.99.5.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh lame
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libogg
  # URL http://downloads.xiph.org/releases/ogg/libogg-1.3.2.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/libogg-1.3.2.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libogg
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  speex
  DEPENDS libogg
  # URL http://downloads.xiph.org/releases/speex/speex-1.2.0.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/speex-1.2.0.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh speex
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-sse  --disable-oggtest --with-ogg=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libvorbis
  DEPENDS libogg
  # URL http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.5.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/libvorbis-1.3.5.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libvorbis
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-oggtest
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libtheora
  DEPENDS libogg
  # URL http://downloads.xiph.org/releases/theora/libtheora-1.2.0alpha1.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/libtheora-1.2.0alpha1.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libtheora
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-oggtest --disable-examples --with-ogg=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  opus
  # URL http://downloads.xiph.org/releases/opus/opus-1.1.4.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/opus-1.1.4.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh opus
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --disable-doc
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  xvidcore
  DEPENDS yasm
  # URL http://downloads.xvid.org/downloads/xvidcore-1.3.4.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/xvidcore-1.3.4.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh xvidcore
  CONFIGURE_COMMAND cd build/generic && PATH=$ENV{PATH} ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_COMMAND cd build/generic && PATH=$ENV{PATH} make
  INSTALL_COMMAND cd build/generic && make install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  x264
  DEPENDS yasm
  # GIT_REPOSITORY git://git.videolan.org/x264.git
  # UPDATE_COMMAND ""
  URL ${CMAKE_SOURCE_DIR}/vendor/x264-git.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh x264
  CONFIGURE_COMMAND PATH=$ENV{PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  x265
  DEPENDS yasm
  # DOWNLOAD_COMMAND hg clone --insecure https://bitbucket.org/multicoreware/x265
  # UPDATE_COMMAND hg update -r stable
  URL ${CMAKE_SOURCE_DIR}/vendor/x265-stable.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh x265
  CONFIGURE_COMMAND cd build/linux && PATH=$ENV{PATH} cmake -G "Unix Makefiles" -D CMAKE_INSTALL_PREFIX:PATH=${CMAKE_BINARY_DIR} -D ENABLE_CLI:BOOL=OFF -D ENABLE_SHARED:BOOL=OFF ../../source
  BUILD_COMMAND cd build/linux && PATH=$ENV{PATH} make
  INSTALL_COMMAND cd build/linux && PATH=$ENV{PATH} make install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libvpx
  DEPENDS yasm
  # URL http://storage.googleapis.com/downloads.webmproject.org/releases/webm/libvpx-1.6.1.tar.bz2
  URL ${CMAKE_SOURCE_DIR}/vendor/libvpx-1.6.1.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libvpx
  CONFIGURE_COMMAND PATH=$ENV{PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  rtmpdump
  DEPENDS zlib openssl
  GIT_REPOSITORY git://git.ffmpeg.org/rtmpdump
  UPDATE_COMMAND ""
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh rtmpdump
  CONFIGURE_COMMAND ""
  BUILD_COMMAND make PREFIX=${CMAKE_BINARY_DIR} SHARED= XCFLAGS=-I${CMAKE_BINARY_DIR}/include XLDFLAGS=-L${CMAKE_BINARY_DIR}/lib XLIBS=-ldl
  INSTALL_COMMAND make PREFIX=${CMAKE_BINARY_DIR} SHARED= install
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  freetype
  # URL http://download.savannah.gnu.org/releases/freetype/freetype-2.7.1.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/freetype-2.7.1.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh freetype
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static --without-png
  INSTALL_COMMAND make install && ln -s ${CMAKE_BINARY_DIR}/include/freetype2 ${CMAKE_BINARY_DIR}/include/freetype2/freetype
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fribidi
  # URL http://fribidi.org/download/fribidi-0.19.7.tar.bz2
  URL ${CMAKE_SOURCE_DIR}/vendor/fribidi-0.19.7.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh fribidi
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  expat
  # URL http://downloads.sourceforge.net/project/expat/expat/2.2.0/expat-2.2.0.tar.bz2
  URL ${CMAKE_SOURCE_DIR}/vendor/expat-2.2.0.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh expat
  CONFIGURE_COMMAND PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  fontconfig
  DEPENDS freetype expat
  # URL http://www.freedesktop.org/software/fontconfig/release/fontconfig-2.12.1.tar.bz2
  URL ${CMAKE_SOURCE_DIR}/vendor/fontconfig-2.12.1.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh fontconfig
  CONFIGURE_COMMAND PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libass
  DEPENDS yasm freetype fribidi fontconfig
  # URL https://github.com/libass/libass/releases/download/0.13.6/libass-0.13.6.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/libass-0.13.6.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libass
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  ffmpeg
  DEPENDS yasm opencore-amr fdkaac lame libogg speex libvorbis libtheora opus xvidcore x264 x265 libvpx rtmpdump freetype libass
  # URL http://www.ffmpeg.org/releases/ffmpeg-3.3.tar.bz2
  URL ${CMAKE_SOURCE_DIR}/vendor/ffmpeg-3.3.tar.bz2
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh ffmpeg
  CONFIGURE_COMMAND PATH=$ENV{PATH} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR} --datadir=${CMAKE_BINARY_DIR}/etc --disable-shared --enable-static --enable-gpl --enable-version3 --enable-nonfree --disable-doc --disable-debug --disable-ffplay --disable-ffserver --disable-outdevs --enable-runtime-cpudetect --extra-cflags=-I${CMAKE_BINARY_DIR}/include\ --static --extra-ldflags=-L${CMAKE_BINARY_DIR}/lib --extra-libs=-lstdc++\ -lexpat\ -ldl --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libfdk-aac --enable-libmp3lame --enable-libspeex --enable-libtheora --enable-libvorbis --enable-libx264 --enable-libx265 --enable-libxvid --enable-libvpx --enable-libopus --enable-librtmp --enable-libfreetype --enable-libass
  BUILD_COMMAND PATH=$ENV{PATH} make
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  libpng
  DEPENDS zlib
  # URL http://download.sourceforge.net/libpng/libpng-1.6.29.tar.gz
  URL ${CMAKE_SOURCE_DIR}/vendor/libpng-1.6.29.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh libpng
  CONFIGURE_COMMAND CFLAGS=-I${CMAKE_BINARY_DIR}/include CPPFLAGS=-I${CMAKE_BINARY_DIR}/include LDFLAGS=-L${CMAKE_BINARY_DIR}/lib ./configure --prefix=${CMAKE_BINARY_DIR} --disable-shared --enable-static
  BUILD_IN_SOURCE 1
)
EXTERNALPROJECT_ADD(
  frmxtract
  DEPENDS ffmpeg libpng
  URL ${CMAKE_SOURCE_DIR}/vendor/frmxtract-3.0.2.tar.gz
  PATCH_COMMAND ${CMAKE_SOURCE_DIR}/patches/patch-manager.sh frmxtract
  CONFIGURE_COMMAND CC=${CMAKE_C_COMPILER} PKG_CONFIG_PATH=$ENV{PKG_CONFIG_PATH} ./configure --prefix=${CMAKE_BINARY_DIR}
  BUILD_IN_SOURCE 1
)
