FROM microsoft/dotnet:1.0.0-preview2-sdk
MAINTAINER Tyler Young <tyler.young@cimplx.com>

# Base in concept on 2020ip/dotnet-mono by Jarrod Alexander <jarroda@2020research.com>
# https://github.com/mono/mono/pull/2771 by manuel-sugawara in mar 2016; original author spouliot in sep 2005
# uses dotnet build -h to trigger initial package cache


COPY nuget /usr/local/bin

ENV PREFIX=/usr/local MONO_THREADS_PER_CPU=50

RUN PATH=$PREFIX/bin:$PATH \
    LIBUV_VERSION=1.9.1 \
    && apt-get update \
    && apt-get install -qqy unzip patch git autoconf libtool libtool-bin automake build-essential gettext gawk parallel pandoc openssl dtrx python libc6 libc6-dev pkg-config libicu-dev \
    && curl -sSL https://github.com/libuv/libuv/archive/v${LIBUV_VERSION}.tar.gz | tar zxfv - -C /usr/local/src \
    && cd /usr/local/src/libuv-$LIBUV_VERSION \
    && sh autogen.sh && ./configure && make && make install \
    && cd .. \
    && rm -rf libuv-$LIBUV_VERSION \
    && ldconfig \
    && curl -sL https://deb.nodesource.com/setup_6.x | bash - \
    && apt-get install -qqy nodejs \
    && npm install npm -g \
    && npm install gulp gulp-sass gulp-concat gulp-clean-css uglify-js jshint gulp-rename gulp-imagemin gulp-autoprefixer grunt-cli bower browserify moment request socket.io ws chalk -g \
    && cd /usr/local/src \
    && git clone --recursive https://github.com/mono/mono.git \
    && cd mono \
    && git checkout tags/mono-4.4.1.0 \
    && wget -q -O - https://patch-diff.githubusercontent.com/raw/mono/mono/pull/2771.patch | patch -p1 --merge \
    && ./autogen.sh --prefix=$PREFIX \
    && make get-monolite-latest \
    && make \
    && make install \
    && cd .. \
    && rm -rf mono \
    && cd ~/ \
    && wget -O ~/nuget.exe https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -nv \
    && wget --max-redirect 0 -O certdata.txt https://hg.mozilla.org/mozilla-central/raw-file/tip/security/nss/lib/ckfw/builtins/certdata.txt -nv \
    && mozroots --import --machine --sync --file certdata.txt \
    && rm -f certdata.txt \
    && ( yes | certmgr -ssl -m https://api.nuget.org ) \
    && ( yes | certmgr -ssl -m https://www.myget.org ) \
    && ( dotnet build -h > /dev/null ) \
    && apt-get -y autoremove \
    && apt-get -y clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && chmod 755 /usr/local/bin/nuget
