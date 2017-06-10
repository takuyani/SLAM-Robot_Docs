# BeagleBone Blackの環境設定
BeagleBone Black（以下、BBBと呼ぶ）の環境構築について説明する。

## 目次  
- 1.&nbsp;[Install Ubuntu 16.04](#1-install-ubuntu-16-04)  
- 2.&nbsp;[Install ROS](#2-install-ros)  
- 3.&nbsp;[Enabling SPI0](#3-enabling-spi0)  

## 1. Install Ubuntu 16.04
BBBにUbuntu 16.04をインストールする。  
インストール方法について色んなサイトで紹介されているため、ここでは省略する。  
以下のサイト等を参考にすること。  
- [ROSエンジニアのブログ](https://crafty-as-a-fox.tumblr.com/post/145100455806/ubuntu1404-1604%E3%81%B8%E7%A7%BB%E8%A1%8C)

## 2. Install ROS
ROSをインストールする。バージョンはKinetic-Kame推奨。  
インストール方法について色んなサイトで紹介されているため、ここでは省略する。  
full版ではなく、base版をインストールしたほうがよい。  

```bash
sudo apt-get install ros-kinetic-ros-base
```
## 3. Enabling SPI0

