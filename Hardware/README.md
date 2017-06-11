# SLAM-Robot Hardware

## 目次  
- 1.&nbsp;[Overview](#1-overview)  
- 2.&nbsp;[Directory Structure](#2-directory-structure)  
- 3.&nbsp;[SLAM Robot](#3-slam-robot)  
    - 3.1.&nbsp;[Assembly](#31-assembly)  
    - 3.2.&nbsp;[BeagleBone Black Environment Configuration](#32-beaglebone-black-environment-configuration)  
        - 3.2.1.&nbsp;[Install Ubuntu](#321-install-ubuntu)
        - 3.2.2.&nbsp;[Install ROS](#322-install-ros)
        - 3.2.3.&nbsp;[Enable Peripheral Device](#323-enable-peripheral-device)

# 1. Overview
SLAM-Robotのハードウェア概要について説明する。
![vehicle1](https://farm9.staticflickr.com/8136/29983598960_4a01cd1490_b.jpg)

# 2. Directory Structure
以下にディレクトリ構成を示す。

    Hardware
     ┣Assembly_Manual
     ┃ ┗Assembly_Manual_JP.md
     ┣BeagleBone_Black
     ┃ ┗dts
     ┃   ┣BB-SPI0-01-00A0.dts
     ┃   ┗BB-SPI0-02-00A0.dts
     ┗Electrical_Circuit_Diagram
       ┗Electrical_Circuit_Diagram.pdf

# 3. SLAM Robot
SLAM評価用対向２輪型移動ロボットの制作方法について説明する。

## 3.1 Assembly
移動ロボット本体の制作方法は以下のファイルを参照。
- [Hardware/Assembly_Manual/Assembly_Manual_JP.md](https://github.com/takuyani/SLAM-Robot_Docs/tree/master/Hardware/Assembly_Manual/Assembly_Manual_JP.md)  

## 3.2 BeagleBone Black Environment Configuration
BeagleBone Black（以下、BBBと呼ぶ）の環境構築について説明する。

### 3.2.1 Install Ubuntu
BBBにUbuntu 16.04をインストールする。  
インストール方法について色んなサイトで紹介されているため、ここでは省略する。  
以下のサイト等を参考にすること。  
- [ROSエンジニアのブログ](https://crafty-as-a-fox.tumblr.com/post/145100455806/ubuntu1404-1604%E3%81%B8%E7%A7%BB%E8%A1%8C)

### 3.2.2 Install ROS
ROSをインストールする。バージョンはKinetic-Kame推奨。  
インストール方法について色んなサイトで紹介されているため、ここでは省略する。  
full版ではなく、base版をインストールしたほうがよい。  

```bash
$ sudo apt-get install ros-kinetic-ros-base
```

### 3.2.3 Enable Peripheral Device
BBBでは以下のペリフェラル・デバイスを使用する。
- SPI0
<br>

上記デバイスを有効にするための手順を以下に示す。

1. ディレクトリ["Hardware/BeagleBone_Black/dts"](https://github.com/takuyani/SLAM-Robot_Docs/tree/master/Hardware/BeagleBone_Black/dts)から以下のDevice Tree Source(DTS)ファイルをダウンロードし、任意の場所に保存する。  
- BB-SPI0-01-00A0.dts
- BB-SPI0-02-00A0.dts
<br>

2. ダウンロードしたDTSファイルをコンパイルする。  
```bash
$ dtc -O dtb -o BB-SPI0-01-00A0.dtbo -b 0 -@ BB-SPI0-01-00A0.dts
$ dtc -O dtb -o BB-SPI0-02-00A0.dtbo -b 0 -@ BB-SPI0-02-00A0.dts
```
コンパイルが成功するとDevice Tree Blob Object(DTBO)ファイルがそれぞれ生成される。

<br>

3. 生成されたDTBOファイルを/lib/firmware/配下にコピーする。  
```bash
$ cp BB-SPI0-01-00A0.dtbo /lib/firmware/
$ cp BB-SPI0-02-00A0.dtbo /lib/firmware/
```
<br>

4. Device Tree Overlay
デバイス・ツリー・オーバーレイを有効にする。（※修正予定）  
```bash
$ echo BB-SPI0-01-00A0 > /sys/devices/bone_capemgr.*/slots
$ echo BB-SPI0-02-00A0 > /sys/devices/bone_capemgr.*/slots
```
<br>

5. 確認（※修正予定）  
```bash
$ sudo chmod 666 /dev/spidev1.0
```

端子モードの確認  
```bash
$ sudo cat /sys/kernel/debug/pinctrl/44e10800.pinmux/pins
```

端子グループの確認  
```bash
$ sudo cat /sys/kernel/debug/pinctrl/44e10800.pinmux/pingroups
```


