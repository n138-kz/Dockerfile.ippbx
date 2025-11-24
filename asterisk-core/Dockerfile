FROM ubuntu:25.10

ARG TZ=Asia/Tokyo
ARG DEBIAN_FRONTEND=noninteractive
ARG LANG=C
ARG LC_ALL=C
ARG LANGUAGE=C

ARG ASTERISK_VERSION=23.1.0
ARG ASTERISK_TARBALL_ASCII=F2FC93DB7587BD1FB49E045A5D984BE337191CE7

RUN pwd;
RUN tree;
RUN tree /media;
RUN test -d /media/conf.d
RUN test -d /media/conf.d/systemd
RUN test -d /media/conf.d/systemd/system

# ライブラリの更新とインストール
RUN apt-get update && apt-get -y upgrade;
RUN apt-get install -y wget;
RUN apt-get install -y gpg;
RUN apt-get install -y tzdata;
RUN apt-get install -y build-essential;
RUN apt-get install -y ca-certificates;
RUN apt-get install -y lsb-release;

# TZ-Data
RUN ln -fs /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN dpkg-reconfigure --frontend noninteractive tzdata

# Asterisk のソースコードをダウンロード
WORKDIR /tmp/
RUN wget -O asterisk.tgz "http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-${ASTERISK_VERSION}.tar.gz";
RUN wget -O asterisk.tgz.asc "http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-${ASTERISK_VERSION}.tar.gz.asc";
RUN gpg --keyserver keyserver.ubuntu.com --recv-keys ${ASTERISK_TARBALL_ASCII};
RUN gpg --verify asterisk.tgz.asc asterisk.tgz;
RUN tar xf asterisk.tgz -C /tmp && mv /tmp/asterisk-* /tmp/asterisk
RUN for i in {0..9}; do echo ''; done

# コンパイル
WORKDIR /tmp/asterisk
RUN ./contrib/scripts/install_prereq install;
RUN ./configure --with-jansson-bundled;
RUN make menuselect;
RUN ./menuselect/menuselect --enable CORE-SOUNDS-JA-WAV menuselect.makeopts;
RUN make -j4;
RUN make install;
RUN make samples;
RUN for i in {0..9}; do echo ''; done

# サービス化
RUN useradd asterisk -s /sbin/nologin
