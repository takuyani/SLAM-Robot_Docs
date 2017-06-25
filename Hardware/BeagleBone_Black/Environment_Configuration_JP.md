# BeagleBone Black環境構築

## 目次  
- 1.&nbsp;[Overview](#1-overview)  
- 2.&nbsp;[Install Ubuntu](#2-install-ubuntu)
- 3.&nbsp;[Install ROS](#3-install-ros)
- 4.&nbsp;[Enable Peripheral Device](#4-enable-peripheral-device)

# 1. Overview
SLAM-Robotを構築する上でのBeagleBone Black（以下、BBBと呼ぶ）環境構築について以下を行っていく。
- Ubuntu 16.04インストール
- ROSインストール
- BBB周辺機能有効

# 2. Install Ubuntu
BBBにUbuntu 16.04をインストールする。  

＜準備するもの＞
- BBB用のUbuntu 16.04のイメージファイル
- Win32DiskImager
- microSD(16GB以上推奨)
- Tera Term(リモートログインで使用)

## 2.1 Creating boot image on SD card
ブートイメージSDカードの作成を行う。

1. 以下のURLにBBB用のUbuntu 16.04のイメージファイルがあるのでダウンロードしておく。  
 xz形式で圧縮されているので7-Zip等で解凍しておく。  
[https://drive.google.com/file/d/0B--eIId_qjJwOXJEdWNBMGxNXzA/view](https://drive.google.com/file/d/0B--eIId_qjJwOXJEdWNBMGxNXzA/view)

2. 解凍したイメージファイルをWin32DiskImagerでmicroSDに書き込む。

3. 書き込みが完了したら、microSDをBBBのカードスロットへ挿入し、HDMIケーブルをディスプレイに接続後、電源を入れる。

4. Ubuntu 16.04がCUIモードで起動すればインストール成功である。

## 2.2 Initial Setting
Ubuntuの初期設定を行う。設定はSSHにより、リモートログインで行っていく。  
よって、事前にMACアドレスを調べ、DHCPサーバ側で割り当てられるIPアドレスを固定にしておくこと。

__1. リモートログイン__  
クライアントPCにTeraTermをインストールし、SSHにて BeagleBone Blackへリモートログイン。  
ubuntuインストール直後では、ユーザ名は「ubuntu」、パスワードは「temppwd」となっている。

__2. 新規ユーザ追加__  
新規にユーザを作成する。
```bash
$ sudo adduser <ユーザ名>
```

 新規に追加したユーザで sudoを使用可能にする。
```bash
$ sudo gpasswd -a <ユーザ名> sudo
```

新しく追加したユーザでログインしsudoできることを確認したら、ユーザubuntuは不要のため削除。
```bash
$ sudo userdel -r ubuntu
```

__3. ホスト名変更__  
ホスト名を変更する。
```bash
$ sudo hostname <ホスト名>
```

/etc/hostnameも編集。
```bash
$ sudo vi /etc/hostname
```

/etc/hostsも変更。
```bash
$ sudo vi /etc/hosts

127.0.0.1 localhost
127.0.1.1 localdomain.arm arm     # この行を変更後のホスト名に修正
# => 127.0.1.1 localdomain.<ホスト名> <ホスト名>
 
# The following lines are desirable for IPv6 capable hosts
::1 localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

最後にrebootし、変更を反映させる。
```bash
$ sudo reboot
```

__4. パーテション拡張__  
デフォルトではubuntuのパーティションが2GB満たない設定となっているため、MicroSDの容量をフルに割り当てる。  

__4.1. パーティションの確認__
```bash
$ df -T

Filesystem     Type     1K-blocks   Used Available Use% Mounted on
/dev/mmcblk0p1 ext4       1811704 797216    920792   9% /     <= ここ
none           tmpfs            4      0         4   0% /sys/fs/cgroup
udev           devtmpfs    236988      4    236984   1% /dev
tmpfs          tmpfs        50272    208     50064   1% /run
none           tmpfs         5120      0      5120   0% /run/lock
none           tmpfs       251352      0    251352   0% /run/shm
none           tmpfs       102400      0    102400   0% /run/user
```

__4.2. パーティション拡張__
```bash
$ sudo fdisk /dev/mmcblk0
 
Command (m for help): p
 
Disk /dev/mmcblk0: 15.7 GB, 15728640000 bytes
128 heads, 23 sectors/track, 10434 cylinders, total 30720000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000
 
        Device Boot      Start         End      Blocks   Id  System
/dev/mmcblk0p1            2048     3749887     1873920   83  Linux
 
Command (m for help): d    <= partition 1 を削除（ここではまだ実際には削除されない）
Selected partition 1
 
Command (m for help): n    <= 新規partitionを作成
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p    <= primary partitionを選択
Partition number (1-4, default 1): 1    <= partition番号は1
First sector (2048-30719999, default 2048):    <= default
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-30719999, default 30719999):  <= default
Using default value 30719999
 
Command (m for help): w     <=  wで実際に設定内容を書き込み
The partition table has been altered!
 
Calling ioctl() to re-read partition table.
 
WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
The kernel still uses the old table. The new table will be used at
the next reboot or after you run partprobe(8) or kpartx(8)
Syncing disks.
```

__4.3. ファイルシステム拡張__
```bash
$ sudo resize2fs /dev/mmcblk0p1
```

__4.4. 確認__
```bash
$ df -T
Filesystem     Type     1K-blocks    Used Available Use% Mounted on
/dev/mmcblk0p1 ext4      15095792 1283624  13167852   9% /     <= 拡張された
none           tmpfs            4       0         4   0% /sys/fs/cgroup
udev           devtmpfs    236988       4    236984   1% /dev
tmpfs          tmpfs        50272     208     50064   1% /run
none           tmpfs         5120       0      5120   0% /run/lock
none           tmpfs       251352       0    251352   0% /run/shm
none           tmpfs       102400       0    102400   0% /run/user
```

__5. スワップ領域の設定__  
BBBはRAMが512MBしかないため、スワップ領域を設定する。
```bash
# スワップ用フォルダの作成
$ sudo mkdir -p /var/cache/swap/
 
# 1Gのスワップ用のファイル作成
$ sudo dd if=/dev/zero of=/var/cache/swap/swapfile bs=1M count=1024
 
# パーミッションの設定
$ sudo chmod 0600 /var/cache/swap/swapfile
 
# スワップ割り当て
$ sudo mkswap /var/cache/swap/swapfile
$ sudo swapon /var/cache/swap/swapfile
```
起動時に自動でスワップが割り当てられるようにするため、/etc/fstabを開き以下を追加、その後にリブートする。
```bash
$ /var/cache/swap/swapfile none swap sw 0 0
```

__6. タイムゾーンの設定__  
タイムゾーンの設定を行う。
```bash
$ sudo dpkg-reconfigure tzdata
```
を実行して、Asia/Tokyoを選択します。

__7. ソフトウェアのアップデート__
```bash
$ sudo apt-get update
$ sudo apt-get upgrade
```

# 3. Install ROS
ROS(kinetic kame)をインストールする。  

__1. sources.listの設定__  
```bash
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
```

__2. 鍵の設定__  
```bash
$ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 0xB01FA116
```

__3. パッケージを最新にアップデート__  
```bash
$ sudo apt-get update
```

__4. ROS のベースセット版をインストール__  
```bash
sudo apt-get install ros-kinetic-desktop-base
```

__5. rosdepの初期化__  
```bash
$ sudo rosdep init
$ rosdep update
```

__6. 環境変数の設定__  
```bash
$ echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
$ source ~/.bashrc
```

__7. rosinstallの準備__  
```bash
$ sudo apt-get install python-rosinstall
```

# 4. Enable Peripheral Device
BBBでは以下のペリフェラル・デバイスを使用する。
- SPI0
<br>

上記デバイスを有効にするための手順を以下に示す。

__1. Device Tree Source(DTS)ファイルのダウンロード__  
ディレクトリ["Hardware/BeagleBone_Black/dts"](https://github.com/takuyani/SLAM-Robot_Docs/tree/master/Hardware/BeagleBone_Black/dts)から以下のDevice Tree Source(DTS)ファイルをダウンロードし、任意の場所に保存する。  
- BB-SPI0-01-00A0.dts
- BB-SPI0-02-00A0.dts
<br>

__2. DTSファイルのコンパイル__  
ダウンロードしたDTSファイルをコンパイルする。  
```bash
$ dtc -O dtb -o BB-SPI0-01-00A0.dtbo -b 0 -@ BB-SPI0-01-00A0.dts
$ dtc -O dtb -o BB-SPI0-02-00A0.dtbo -b 0 -@ BB-SPI0-02-00A0.dts
```
コンパイルが成功するとDevice Tree Blob Object(DTBO)ファイルがそれぞれ生成される。  
生成されたDTBOファイルを/lib/firmware/配下にコピーする。  
```bash
$ cp BB-SPI0-01-00A0.dtbo /lib/firmware/
$ cp BB-SPI0-02-00A0.dtbo /lib/firmware/
```

__3. Device Tree Overlay__  
デバイス・ツリー・オーバーレイを有効にする。（※修正予定）  
```bash
$ echo BB-SPI0-01-00A0 > /sys/devices/bone_capemgr.*/slots
$ echo BB-SPI0-02-00A0 > /sys/devices/bone_capemgr.*/slots
```

__4. 確認（※修正予定）__  
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


# <参考>
1. [BeagleBone BlackにUbuntu 14.04をインストール](http://shirokuma-web.net/bbb-ubuntu-setup/)
2. [Beaglebone Black: Ubuntu 14.04 + ROS (Indigo) install](https://fleshandmachines.wordpress.com/2015/08/25/beaglebone-black-ubuntu-14-04-ros-indigo-install/)
3. [Ubuntu install of ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu)
