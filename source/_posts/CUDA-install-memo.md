---
title: CUDA install memo
tags:
  - centos
  - cuda
  - gpu
categories:
  - lab
date: 2014-11-06 00:00:00
---
CUDA-install-memo
<!-- more -->
CUDAの環境を確認
{% vimhl bash %}
[root@miro ~]# yum install pciutils

[root@miro ~]# lspci | grep -i nVidia
02:00.0 VGA compatible controller: NVIDIA Corporation GK110 [GeForce GTX Titan] (rev a1)
02:00.1 Audio device: NVIDIA Corporation GK110 HDMI Audio (rev a1)
03:00.0 VGA compatible controller: NVIDIA Corporation GK110 [GeForce GTX Titan] (rev a1)
03:00.1 Audio device: NVIDIA Corporation GK110 HDMI Audio (rev a1)
{% endvimhl %}

rpmforgeのためのセットアップを行います。yum を用いてリポジトリのセットを行います。
{% vimhl bash %}
#!bash
[root@miro ~]# yum install wget

[root@miro ~]# mkdir cuda_tmp

[root@miro ~]# cd cuda_tmp

[root@miro ~]# wget http://developer.download.nvidia.com/compute/cuda/repos/rhel6/x86_64/cuda-repo-rhel6-6.5-14.x86_64.rpm

[root@miro ~]# yum install http://pkgs.repoforge.org/rpmforge-release/rpmforge-release-0.5.3-1.el6.rf.x86_64.rpm
{% endvimhl %}
[Run level 3 に戻す](http://www.softek.co.jp/SPG/Pgi/TIPS/public/accel/cuda65_install.html)
{% vimhl bash %}
#!bash
[root@miro ~]# init 3
{% endvimhl %}
RPM によるインストール
{% vimhl bash %}
[root@miro cuda_tmp]# rpm --install cuda-repo-rhel6-6.5-14.x86_64.rpm

警告: cuda-repo-rhel6-6.5-14.x86_64.rpm: ヘッダ V3 DSA/SHA1 Signature, key ID 5c37d3be: NOKEY

[root@miro cuda_tmp]# ls /etc/yum.repos.d/
CentOS-Base.repo       CentOS-Media.repo  cuda.repo         mirrors-rpmforge-extras   rpmforge.repo
CentOS-Debuginfo.repo  CentOS-Vault.repo  mirrors-rpmforge  mirrors-rpmforge-testing

[root@miro cuda_tmp]# yum clean expire-cache
Loaded plugins: fastestmirror
Cleaning repos: base cuda extras rpmforge updates
6 metadata files removed

[root@miro cuda_tmp]# yum install cuda

Error: Package: 1:xorg-x11-drv-nvidia-libs-340.29-1.el6.x86_64 (cuda)
           Requires: libvdpau(x86-64) >= 0.5
           Available: libvdpau-0.4.1-1.el6.rf.x86_64 (rpmforge)
               libvdpau(x86-64) = 0.4.1-1.el6.rf
Error: Package: 1:xorg-x11-drv-nvidia-libs-340.29-1.el6.i686 (cuda)
           Requires: libvdpau(x86-32) >= 0.5
{% endvimhl %}
libvdpauパーケージのバージョンを上げる
{% vimhl bash %}
[root@miro cuda_tmp]# yum update libvdpau
...
Setting up Update Process
Package(s) libvdpau available, but not installed.
No Packages marked for Update

[root@miro cuda_tmp]# vi /etc/yum.repos.d/linuxtech.repo

[linuxtech]
name=LinuxTECH
baseurl=http://pkgrepo.linuxtech.net/el6/release/
enabled=1
gpgcheck=1
gpgkey=http://pkgrepo.linuxtech.net/el6/release/RPM-GPG-KEY-LinuxTECH.NET

[root@miro cuda_tmp]# yum install libvdpau

[root@miro cuda_tmp]# yum list installed | grep libvdpau
libvdpau.x86_64        0.5-2.el6        @linuxtech
{% endvimhl %}
cuda をインストール
{% vimhl bash %}
[root@miro cuda_tmp]# yum install cuda
...
Complete!
{% endvimhl %}
zshが入ってない．．．

{% vimhl bash %}
[root@miro ~]# echo $SHELL
/bin/bash

[root@miro ~]# yum install zsh

[root@miro ~]# cat /etc/shells
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/zsh

[root@miro ~]# chsh
root のシェルを変更します。
新しいシェル [/bin/bash]: /bin/zsh
シェルを変更しました。

[root@miro]~# echo $SHELL
/bin/zsh

[root@miro]~# vi .zshrc

autoload -U compinit
compinit
setopt auto_cd
setopt auto_pushd
setopt correct
{% endvimhl %}
環境変数設定
{% vimhl bash %}
#!bash
[root@miro]~# vi .zshrc

export PATH=/usr/local/cuda-6.5/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-6.5/lib64:/lib:$LD_LIBRARY_PATH
{% endvimhl %}
Test
{% vimhl bash %}
[root@miro]~# which nvcc
/usr/local/cuda-6.5/bin/nvcc

[root@miro]~/cuda_tmp# nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2014 NVIDIA Corporation
Built on Thu_Jul_17_21:41:27_CDT_2014
Cuda compilation tools, release 6.5, V6.5.12

[root@miro]~/cuda_tmp# vi test.c

main(){
        printf("hello\n");
}

[root@miro]~/cuda_tmp# nvcc test.c
test.c: In function ‘main’:
test.c:2: 警告: incompatible implicit declaration of built-in function ‘printf’

[root@miro]~/cuda_tmp# ./a.out
hello
{% endvimhl %}

結局deviceQueryが動かない
ドライバが見つからないという落ちでした．

~~あしたつづく~~．．．
***
なんと．．．できました．．．

NVIDIA公式のドキュメントの方法だとうまくいかなかった, rpm　形式じゃなく，run でインストール

たくさん試してみたが．．たぶんこんな感じです．



まず，gcc wget がyumで入れる, wgetでcuda_6.5.14_linux_64.runをDL

{% vimhl bash %}
yum install -y kernel-devel

nouveauを無効化ため/etc/grub.conf の中の記述に rdblacklist=nouveau を追加する。

gdmを停止  init 3

chmod +x cuda_6.5.14_linux_64.run

./cuda_6.5.14_linux_64.run --kernel-source-path=/usr/src/kernels/2.6.32-504.el6.x86_64

あとは環境変数などの設定だけ
{% endvimhl %}
***

p.s. ドライバの単独インストールはいらない
環境変数などの設定なんですが，/etc/profile.d/ 以下にシェルスクリプトとして記述すると，SHELLを変更しても自動的に読み込みらしい
{% vimhl bash %}
vi /etc/profile.d/cuda.sh

export PATH=$PATH:/usr/local/cuda-5.5/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-5.5/lib64:/lib
{% endvimhl %}
参考したサイト:

[CentOS6.5にCUDA 6.0をインストール | CUDAやってみる！](http://cuda.dogrow.net/?p=42)
