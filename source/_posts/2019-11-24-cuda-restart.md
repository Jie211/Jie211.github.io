---
title: CUDA再入门 - 环境搭建
date: 2019-11-24 13:50:14
tags:
  - cuda
categories:
  - it
keywords: cuda, eclips, nsight
description: CUDA再入门
---

在学校的时候虽然学习过 CUDA，但是总是要忙着打工并且有论文的压力，没能静下心来仔细的学好 CUDA，所以现在准备从零开始 CUDA 再入门

<!-- more -->

<!-- toc -->

# 驱动和 CUDA Toolkit

之前一直认为装驱动是个很麻烦的事情。后来才知道那只是存在于 CentOS 的状况，当然可能别的 LinuxOS 也是如此，但是 Ubuntu 上装驱动真是简单到爆。所以在家里我果断的选择了以 Ubuntu 为基础的 elementaryOS，Ubuntu 上装 Nvidia 的驱动几乎是自动的，你只要用的是 N 卡自动就会装上 N 卡驱动了。

OK，于是接下来是 CUDA Toolkit，其本质是各类头文件，库和 nsight 相关程序还有 sample code。在下面的官网直接下载就行，现在的最新版本是 10.2 所以我选择了 Linux x86_64 Ubuntu 18.04 的 deb[local]。
[CUDA Toolkit 10.2 Download \| NVIDIA Developer](https://developer.nvidia.com/cuda-downloads)
就是个 deb 文件，按照提示安装就好

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget http://developer.download.nvidia.com/compute/cuda/10.2/Prod/local_installers/cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu1804-10-2-local-10.2.89-440.33.01_1.0-1_amd64.deb
sudo apt-key add /var/cuda-repo-10-2-local-10.2.89-440.33.01/7fa2af80.pub
sudo apt-get update
sudo apt-get -y install cuda
```

顺便我还准备在 MacOS 上也装上 Toolkit，虽然没有 N 卡 GPU 不能执行，但是有 Toolkit 就能编译了(虽然 clang 和 CUDA 之间还挺麻烦)，起码写代码能静态检查文法错误。MacOS 上装 Toolkit 就是个 dmg，所以普通的安装就行。
最好加上 path 就 ok。

```bash
export PATH=/Developer/NVIDIA/CUDA-10.2/bin${PATH:+:${PATH}}
export DYLD_LIBRARY_PATH=/Developer/NVIDIA/CUDA-10.2/lib${DYLD_LIBRARY_PATH:+:${DYLD_LIBRARY_PATH}}
```

# nsight

这个真的是太坑了，因为 nsight 对 java 支持上的问题导致一般来说是不能正常启动的。

## Ubuntu

首先要装 java8 的 jre，linuxbrew 也行别的方法也行，最重要的是把 jre 放在 path 能通的地方

```bash
export PATH="/usr/lib/jvm/java-8-openjdk-amd64/jre/bin:$PATH"
```

或者就必须要在启动 nsight 的时候加上参数了，可以设成 alias。但是这样之后在图形化界面里通过图标启动就没用了。所以还是加 path 吧。

```bash
alias nsight='nsight -vm /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java'
```

## MacOS

### 安装

这个真是坑到炸，因为如果你 java 版本太高，你打开 nsight 的时候他不会推出，而是会 freeze，你还以为是别的原因导致的。。。
但其实官方说明里说的很清楚了

<div class="tip">
Note: JRE 1.8u152 or later is not supported for Nsight Eclipse Edition or Visual Profiler. You can find the JRE update 151 on the Oracle Download Archive site here: https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html?printOnly=1.
</div>

[Nsight Eclipse Edition :: CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/nsight-eclipse-edition-getting-started-guide/index.html)

需要 JRE1.8u151 或以下的版本才行，如果装了更高的版本那就只有暂时把新版本删除之后下载旧的版本了

```bash
sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
sudo rm -fr /Library/PreferencePanes/JavaControlPanel.prefPane
sudo rm -fr ~/Library/Application\ Support/Oracle/Java
```

下载 JRE 或者 JDK 其实都行 Oracle 官方的下载越来越恶心了，需要注册什么的。
这个 repo 提供全平台 JDK 和 JRE 的分流，毕竟不是官方的下载 所以下载之后最好对比下哈希值比较安全。
[GitHub - frekele/oracle-java: Oracle Java Binaries](https://github.com/frekele/oracle-java)

### 编译

也是坑，因为 nsight/eclipse 好像不知道你的 c/c++的 include path 在哪，所以需要手动加上去。新建项目后，对项目点右键，properties -> C/C++ General -> paths and Symbols > Add
加上 c/c++的 include path 就行，path 可以通过对任意 c 或者 c++代码进行
编译时加上 verbose 的选项，在显示出来的冗余信息里就有

```bash
gcc -v hoge.c
g++ -v hoge.c
```

### clang 的问题

clang 11.0.0 和 nvcc 合不来。。。
因为官方只支持 xcode10.1 和 clang/llvm10.0.0
解决方法应该是装低版本的 `Command_Line_Tools_macOS_10.13_for_Xcode_10.1.dmg`

而且还能切换版本

```bash
# install version A
mkdir -p ~/Command_Line_Tools/xcode_A
sudo cp -R /Library/Developer/ ~/Command_Line_Tools/xcode_A
# install version B
mkdir -p ~/Command_Line_Tools/xcode_B
sudo cp -R /Library/Developer/ ~/Command_Line_Tools/xcode_B
# switch
sudo xcode-select -s ~/Command_Line_Tools/xcode10_13/Developer/CommandLineTools
clang --version
Apple LLVM version 10.0.0 (clang-1000.10.44.4)
Target: x86_64-apple-darwin18.7.0
Thread model: posix
InstalledDir: /Users/gchen/Command_Line_Tools/xcode10_13/Developer/CommandLineTools/usr/bin

sudo xcode-select -s ~/Command_Line_Tools/xcode11_2/Developer/CommandLineTools
clang --version
Apple clang version 11.0.0 (clang-1100.0.33.12)
Target: x86_64-apple-darwin18.7.0
Thread model: posix
InstalledDir: /Users/gchen/Command_Line_Tools/xcode11_2/Developer/CommandLineTools/usr/bin
```

而且 LLVM 和 nvcc 之间也有很多用法上的区别，详细可以看这个
[Compiling CUDA with clang — LLVM 10 documentation](https://llvm.org/docs/CompileCudaWithLLVM.html)

```bash
# 不指定
☁  ~  nvcc -v hoge.c
nvcc fatal   : The version ('11.0') of the host compiler ('Apple clang') is not supported
# 通过homebrew装的gcc9
☁  ~  nvcc -ccbin /usr/local/bin/gcc -v hoge.c
nvcc fatal   : GNU C/C++ compiler is no longer supported as a host compiler on Mac OS X.
# 苹果的gcc其实是clang
☁  ~  nvcc -ccbin /usr/bin/gcc -v hoge.c
nvcc fatal   : The version ('11.0') of the host compiler ('Apple clang') is not supported
# 苹果的clang
☁  ~  nvcc -ccbin /usr/bin/clang -v hoge.c
nvcc fatal   : The version ('11.0') of the host compiler ('Apple clang') is not supported
```
