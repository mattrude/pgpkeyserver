---
layout: default
title: GnuPG 2.1.10 Build Instructions
permalink: /guides/build-gnupg2/
description: My GnuPG 2.1.10 build for a Ubuntu 14.04 LTS.
tags: PGP, GnuPG
---

# GnuPG 2.1.10 Build Instructions
Below you is my build instructions for [GnuPG 2.1.10](https://lists.gnupg.org/pipermail/gnupg-announce/2015q4/000381.html) released on 4-Dec-2015. These instructions are built for a [Ubuntu](http://www.ubuntu.com/server) 14.04 LTS server.

## Install the needed depends
    apt-get -y install libgnutls-dev pinentry-qt bzip2 make

## Setup the build
    ln /usr/bin/pinentry /usr/local/bin/
    mkdir -p /var/src/gnupg21 && cd /var/src/gnupg21
    gpg --recv-keys 0x4F25E3B6 0xE0856959 0x33BD3F06 0x7EFD60D9

## Installing libgpg-error
    wget ftp://ftp.gnupg.org/gcrypt/libgpg-error/libgpg-error-1.20.tar.gz
    wget ftp://ftp.gnupg.org/gcrypt/libgpg-error/libgpg-error-1.20.tar.gz.sig
    gpg --verify libgpg-error-1.20.tar.gz.sig && tar -xzf libgpg-error-1.20.tar.gz && \
    cd libgpg-error-1.20/ && ./configure && make && make install && cd ../

## Installing libgcrypt
    wget ftp://ftp.gnupg.org/gcrypt/libgcrypt/libgcrypt-1.6.4.tar.gz && \
    wget ftp://ftp.gnupg.org/gcrypt/libgcrypt/libgcrypt-1.6.4.tar.gz.sig && \
    gpg --verify libgcrypt-1.6.4.tar.gz.sig && tar -xzf libgcrypt-1.6.4.tar.gz && \
    cd libgcrypt-1.6.4 && ./configure && make && make install && cd ../

## Installing libassuan
    wget ftp://ftp.gnupg.org/gcrypt/libassuan/libassuan-2.4.2.tar.bz2 && \
    wget ftp://ftp.gnupg.org/gcrypt/libassuan/libassuan-2.4.2.tar.bz2.sig && \
    gpg --verify libassuan-2.4.2.tar.bz2.sig && tar -xjf libassuan-2.4.2.tar.bz2 && \
    cd libassuan-2.4.2 && ./configure && make && make install && cd ../

## Installing libksba
    wget ftp://ftp.gnupg.org/gcrypt/libksba/libksba-1.3.3.tar.bz2 && \
    wget ftp://ftp.gnupg.org/gcrypt/libksba/libksba-1.3.3.tar.bz2.sig && \
    gpg --verify libksba-1.3.3.tar.bz2.sig && tar -xjf libksba-1.3.3.tar.bz2 && \
    cd libksba-1.3.3 && ./configure && make && make install && cd ../

## Installing npth
    wget ftp://ftp.gnupg.org/gcrypt/npth/npth-1.2.tar.bz2 && \
    wget ftp://ftp.gnupg.org/gcrypt/npth/npth-1.2.tar.bz2.sig && \
    gpg --verify npth-1.2.tar.bz2.sig && tar -xjf npth-1.2.tar.bz2 && \
    cd npth-1.2 && ./configure && make && make install && cd ../

## Install GnuPG 2.1
    wget ftp://ftp.gnupg.org/gcrypt/gnupg/gnupg-2.1.10.tar.bz2 && \
    wget ftp://ftp.gnupg.org/gcrypt/gnupg/gnupg-2.1.10.tar.bz2.sig && \
    gpg --verify gnupg-2.1.10.tar.bz2.sig && tar -xjf gnupg-2.1.10.tar.bz2 && \
    cd gnupg-2.1.10 && ./configure && make && make install && echo $?

## Finishing the build
    echo "/usr/local/lib" > /etc/ld.so.conf.d/gpg2.conf && ldconfig -v
