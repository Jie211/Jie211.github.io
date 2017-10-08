---
title: replace_CUDA
tags:
  - cuda
  - nvidia
  - gpu
  - driver
  - centos
categories:
  - lab
date: 2015-05-30 00:00:00
---

計算機のreplaceなのでOSからやり直す
<!-- more -->
**5/28**
centosのminimalイメージをダウンロード

usbをFATでフォーマット

UnetBootinでイメージ変換

インストール

ファイルが足りない見たい．．．．

WTF...
明日続く．．．

**5/29**

UnetBootinをやめる


#USBイメージを作る

    #!bash
    //isoをimgに変換
    hdiutil convert -format UDRW -o TO.img FROM.iso
    //disk番号を確認
    diskutil list
    //usbをunmount
    diskutil unmountDisk /dev/diskN
    //変換されたイメージをusbに入れる
    sudo dd if=IMAGE.img.dmg of=/dev/diskN bs=1m
    //途中はcontrol+tで時間を確認

    //終わったら取り出し
    diskutil eject /dev/diskN

#centosをインストール

    #!bash
    /etc/sysconfig/network-scripts/ifcfg-eth0を編集
    //http://qiita.com/uemura/items/67bafacb03b17fadfb5b参照
    DEVICE=eth0
    ONBOOT=yes
    HWADDR=XXXXXXXXXXXXXX
    TYPE=Ethernet
    BOOTPROTO=none
    IPADDR=XXXXXXXXXXXXX
    PREFIX=24
    GATEWAY=XXXXXXXXXXX
    DNS1=XXXXXXXXXXXX
    DNS2=XXXXXXXXXXXX
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=yes
    IPV6INIT=no
    UUID=XXXXXXXXXXXXXXXXXX

    // /etc/sysconfig/networkを編集
    NETWORKING=yes
    HOSTNAME=XXXXXXXX.net
    GATEWAY=XXXXXXXXXX

    // /etc/resolv.confを編集
    nameserver XXXXXXXXXXXXXXXX

    service network restart

    // 開発環境を入れる
    yum groupinstall "Development Tools"
    yum install pciutils
    yum install wget

    // GPUを確認
    lspci | grep -i nvidia

    // OS確認
    uname -m && cat /etc/*release

#CUDA

    #!bash
    // CUDAドライバをwgetでダウンロード

    // nouveauの存在を確認
    lsmod | grep nouveau　

    // vi /etc/modprobe.d/blacklist_nouveau.confに書き込む
    blacklist nouveau
    options nouveau modeset0

    init 3

    // Next create a new “initramfs” file and taking backup of existing.

    mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
    dracut -v /boot/initramfs-$(uname -r).img $(uname -r)

    reboot -h now

    // nouveauがロードされていないことを確認
    lsmod | grep nouveau　

    // インストール開始
    sh ./cuda_xx.run

    //失敗

    Driver:   Installation Failed
    The driver installation is unable to locate the kernel source. Please make sure that the kernel source packages are installed and set up correctly.


    // http://qiita.com/metheglin/items/60261f474ccdfb467574　参照して直す

**8/28memo**

ここはおかしい
参照にサイトではkernelとkernel-develが一致しないからkernelのバージョンを基準でkerel-develを古いバージョンにした．別にそうする必要がない kernelをdevelと同じ新しいバージョンにした方がいいと思う．
一致しない理由は多分Development Toolsを入れた時kernel-develだけ更新した．なのでまず一応kernel-develを削除する

    #!bash
    KERNEL_DEVEL_WRONG_VERSION=`rpm -qa | grep kernel-devel`
    echo ${KERNEL_DEVEL_WRONG_VERSION}

    rpm -e --nodeps ${KERNEL_DEVEL_WRONG_VERSION}

    //次yum install kernel kernel-develで入れればいい

    sh ./cuda_xx.run

    //成功

    Driver:   Installed
    Toolkit:  Installed in /usr/local/cuda-7.0
    Samples:  Installed in /root, but missing recommended libraries

    vi /etc/profile.d/cuda.sh

    export PATH=$PATH:/usr/local/cuda-X.X/bin
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-X.X/lib64:/lib

#gnomeのGUI

    #!bash
    yum  groupinstall "X Window System" "Desktop" "General Purpose Desktop"

    **8/28memo**
    //まず，/dev/のしたにnvidia*がない場合は次のスクリプトを実行

    #!/bin/bash
    /sbin/modprobe nvidia
    if [ "$?" -eq 0 ]; then
      # Count the number of NVIDIA controllers found.
      NVDEVS=`lspci | grep -i NVIDIA`
      N3D=`echo "$NVDEVS" | grep "3D controller" | wc -l`
      NVGA=`echo "$NVDEVS" | grep "VGA compatible controller" | wc -l`
      N=`expr $N3D + $NVGA - 1`
      for i in `seq 0 $N`; do
        mknod -m 666 /dev/nvidia$i c 195 $i
      done
      mknod -m 666 /dev/nvidiactl c 195 255
    else exit 1
    fi
    /sbin/modprobe nvidia-uvm
    if [ "$?" -eq 0 ]; then
      # Find out the major device number used by the nvidia-uvm driver
      D=`grep nvidia-uvm /proc/devices | awk '{print $1}'`
      mknod -m 666 /dev/nvidia-uvm c $D 0
    else
    exit 1
    fi

    nvidia-xconfigを実行する


    // するとstartxでguiで入るのはず


    //simpleGLをmake
    //libGL.so libGLU.so libX11.so libXi.so libXmu.soが見つからないのエラー

    yum install freeglut freeglut-devel mesa-libGLU-devel libX11-devel libXmu-devel libXi-devel

    //次はcudaを再インストールする

    //CUDAのサンプルをコンパイルするために
    //sample の　Makefileに LIBRARIES += -L/usr/lib64/
    //finde のGLLIB GLULIB X11LIB XILIB XMULIB のif文の下のSAMPLE_ENABLED := 0をコメントアウト

    するとコンパイルが通るし，ローカルではsimpleGLが実行できるのはず


#VNC

    #!bash
    //しかし，sshのXForwardingでリモートログインするとglxgearなどのOpenGL処理がすごい重い，sampleGLが実行できない

    //じゃsshじゃなくてvncを使ってみる

    yum install tigervnc-server

    //5901portを開く
    vi /etc/sysconfig/iptables

    -A INPUT -m state --state NEW -m tcp -p tcp --dport 5901 -j ACCEPT

    //5901:5910全部開いてもいい

    service iptables restart

    su -test
    vpncpasswd

    vncserver:1 //1番ウィンドウを起動

    vncserver -kill :1 //kill

    vi .vnc/xstartup
    twm &//この行コマンドアウト
    exec gnome-session & //追加

    vncserver :1 -geometry 1366x768 -depth 24

    //mac ->Finder ->移動 -> サーバーへ接続
    vnc://192.168.100.XXX:5901

    //portは5900にウィンドウナンバー

    //デスクトップがきた．．．

    //ええええ．．．文字化け．．．

    //rootで
    yum groupinstall  “Japanese Support”　“fonts”

    //simpleGLを実行
    //extension "GLX" missing on display ":1.0"
    //freeglut(./simgpleGL): OpenGL GLX extension not supported by display ':1.0'

    //glxinfoを実行
    //extension "GLX" missing on display ":1.0"
    //Error couldn't find RGB GLX visual or fbconfig

    //WTF!!!
    //VirtualGLを使う

**5/30**

#VirtualGL

    #!bash
    //OpenGLのことに戻る
    //VirtualGLを導入してみる

    //HPのDocumentを参照　
    //Server側
    //まず，rpmをダウンロードhttp://sourceforge.net/projects/virtualgl/files

    rpm -e VirtualGL
    rpm -i VirtualGL*.rpm

    //Client側はmacなのでdmgで入れる

    //Server側
    init 3
    /opt/VirtualGL/bin/vglserver_config

    //n-> n-> n

    init 5

    xdpyinfo -display :0
    /opt/VirtualGL/bin/glxinfo -display :0 -c | grep OpenGL

    //OpenGL version string: 4.5.0 NVIDIA 346.46

    //いい感じ


    //XForwardingを使う
    //mac上X11起動
    //自分のterminalを起動
    //terminalで
    /opt/VirtualGL/bin/vglconnect user@server
    //server側で
    /optVirtualGL/bin/vglrun ./simpleGL

    //できた！！！！！！

    //VNCを使う場合
    //VNCでserverに入る
    /optVirtualGL/bin/vglrun ./simpleGL


#OpenCL

    #!bash
    //OpenCLを入れてみる
    //NvidiaのGPU側はCUDAドライバがあればOKみたい
    //CPU側は

    //Intel Code Builder for OpenCL API

    //install builder & runtimes
    //をダウンロードしてインストール
    https://software.intel.com/en-us/articles/intel-code-builder-for-opencl-api
    $ yum install numactl*
    $ yum install redhat-lsb-core

    //OK...一応
    サンプル http://www.fixstars.com/files/opencl/samples2.zip
