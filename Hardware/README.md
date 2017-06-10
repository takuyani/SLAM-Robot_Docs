# SLAM-Robot Hardware

## 目次  
- 1.&nbsp;[Overview](#1-overview)  
- 2.&nbsp;[Directory Structure](#2-directory-structure)  
- 3.&nbsp;[SLAM Robot](#3-slam-robot)  
    - 3.1.&nbsp;[Assembly](#31-assembly)  
    - 3.2.&nbsp;[BeagleBone Black Environment Configuration](#32-beaglebone-black-environment-configuration)  
        - 3.2.1.&nbsp;[Install Ubuntu](#321-install-ubuntu)
        - 3.2.2.&nbsp;[Install ROS](#322-install-ros)
        - 3.2.3.&nbsp;[Enable SPI0](#323-enable-spi0)

## 1. Overview
概要

## 2. Directory Structure
ディレクトリ構成

    ▼Hardware
    ┣▼Assembly_Manual
    ┃ ・Assembly_Manual_JP.md
    ┣▼BeagleBone_Black
    ┃┗▼dts
    ┃  ・BB-SPI0-01-00A0.dts
    ┃  ・BB-SPI0-02-00A0.dts
    ┗▼Electrical_Circuit_Diagram
      ・Electrical_Circuit_Diagram.pdf

## 3. SLAM Robot
SLAM評価用対向２輪型移動ロボットの制作方法について説明する。

### 3.1 Assembly
移動ロボット本体の制作方法は以下のファイルを参照。
- [Hardware/Assembly_Manual/Assembly_Manual_JP.md](https://github.com/takuyani/SLAM-Robot_Docs/tree/master/Hardware/Assembly_Manual/Assembly_Manual_JP.md)  

### 3.2 BeagleBone Black Environment Configuration
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
sudo apt-get install ros-kinetic-ros-base
```

### 3.2.3 Enable SPI0













