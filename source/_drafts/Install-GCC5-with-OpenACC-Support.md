---
title: Install_GCC5_with_OpenACC_Support
tags:
  - cuda
  - nvidia
  - gpu
  - driver
  - centos
categories:
  - lab
date: 2015-07-06 00:00:00
---

Installation of GCC 5 with OpenACC support memo
<!-- more -->

インストール手順は
http://scelementary.com/2015/04/25/openacc-in-gcc.html
を参照
ただ，
build nvptx-tools のoption
runで入れたCUDAには/usr/lib64にnvidiaがないため

    #!bash
    --with-cuda-driver-lib=/usr/lib64/nvidia —> --with-cuda-driver-lib=/usr/lib64

**下は変更した点とエラー対処のメモ**

GCCのLATEST-5-SNAPSHOTかGCCの安定版を使う
mirror http://ftp.tsukuba.wide.ad.jp/software/gcc/

**!!7/6メモ!!**

latestのgcc5.1.1が実行するときldに関するエラーが出ったため，安定版の5.1.0を使ったら解決した．

CCとCXXの変数は環境によって変更する今回はgcc48を使った

**!!8/28メモ!!**

gcc5.2.0が出たので新しいの安定版を使ってみた
configureの段階でconfigure: error: Building GCC requires GMP 4.2+, MPFR 2.4.0+ and MPC 0.8.0+.

それぞれのライブラリを先にインストール必要があるということです．

    #!bash
    GMP
    ./configure --disable-shared --enable-static --prefix=/install/GMP
    make && make check && make install

    MPFR
    ./configure --disable-shared --enable-static --prefix=/install/MPFR --with-gmp=/install/GMP
    make && make check && make install

    MPC
    ./configure --disable-shared --enable-static --prefix=/install/MPC --with-gmp=/install/GMP --with-mpfr=/install/MPFR
    make && make check && make install

offloadcompilerとgccをconfigureする時は次のように追加する

    #!bash
      --with-gmp=/install/GMP \
      --with-mpfr=/install/MPFR \
      --with-mpc=/install/MPC \

次のエラーなら“Development tools”をgroupinstallする

    #!bash
    g++: error: gengtype-lex.c: No such file or directory
    g++: fatal error: no input files

次のエラーならnewlibはgccと一緒にコンパイルされるって書いていた，まず，newlibを単独でmakeしてみる

    #!bash
    configure: error: in `/build/gcc5-accel/nvptx-none/newlib':configure: error: C compiler cannot create executables

さらにnewlibをコンパイルするときに次のエラーならtexinfo.x86_64をyumで入れる

    #!bash
    `makeinfo' is missing on your system.


/installにあるgccのファイルをGCCXXに入れ(XXはバージョン)
パスを通す，また複数gccの共存には新しいgccとg++をgccXX g++XXに名前を変更すればいいみたい

    #!bash
    export PATH=/install/GCC52/bin:$PATH
    export LIBRARY_PATH=/install/GCC52/lib64:$LIBRARY_PATH
    export LD_LIBRARY_PATH=/install/GCC52/lib64:$LD_LIBRARY_PATH
    export CPLUS_INCLUDE_PATH=/install/GCC52/include/c++/5.0.1:$CPLUS_INCLUDE_PATH
    export MANPATH=/install/GCC52/share/man:$MANPATH
