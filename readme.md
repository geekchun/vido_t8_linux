## 原道T8安装ubuntu记录

### 硬件概况

| PCB MASK | MC03-D-V200                |
| -------- | -------------------------- |
| CPU      | Z3735F                     |
| DRAM     | 2G DDR3                    |
| EMMC     | 32G                        |
| 声卡     | RT5640                     |
| 有线网卡 | DM9601                     |
| 无线网卡 | AP6212                     |
| 触摸IC   | ICN8525(ICN8505)           |
| 显示屏   | MIPI DSI 800x1280 默认竖屏 |

### 安装记录

分别测试了以下版本，触摸固件使用windows提取的fw，无线网卡使用网上找的bin

| 系统版本        | 情况                                                     |
| --------------- | -------------------------------------------------------- |
| 18.04 Desktop   | 鼠标颠倒，有线网卡和声卡无法驱动，联网安装可引导         |
| 20.04.6 Desktop | 鼠标颠倒，声卡无法驱动，联网安装可引导                   |
| 22.04.5 Desktop | 鼠标颠倒，有线网卡和声卡正常，联网安装不可引导           |
| 24.04.3 Desktop | 鼠标颠倒，有线网卡和声卡正常，内存占用1.5G，无法正常使用 |
| 25.04 Desktop   | 鼠标正常，有线网卡和声卡正常，内存占用1.5G，无法正常使用 |

目前驱动最接近完美的版本是22.04.5，除鼠标箭头颠倒外，其他驱动正常

实测18和20在联网安装的情况下可以正常安装引导程序，其他版本需要手动修复引导

### 手动修复版安装方法

1. 使用Rufus烧录镜像到优盘，选择ISO模式，镜像用20.04.6

2. 拷贝bootia32.efi到优盘/EFI/boot

3. 正常进行系统安装，会提示grub安装失败，此时手动拷贝i386-efi到/boot/grub下，重启

4. 重启会进入grub命令行，按照以下命令进行手动引导。

   (hd0,gpt2)是rootfs所在分区 

   vmlinuz需要tab补全

   linux这行必须指定实际root分区，也就是EMMC第二个分区

   > set root=(hd0,gpt2)
   >
   > linux /boot/vmlinuz-* root=/dev/mmcblk1p2
   >
   > initrd /boot/initrd.img
   >
   > boot

   此时即可进入系统，按照以下命令进行引导修复，重启后可自动引导进系统

   > sudo apt-get remove --purge --allow-remove-essential shim-signed
   >
   > sudo apt-get remove --purge grub-efi-amd64 grub-efi-amd64-bin grub-efi-amd64-signed
   >
   > sudo apt-get -y install grub-efi-ia32-bin grub-efi-ia32 grub-common grub2-common
   >
   > sudo grub-install --target=i386-efi /dev/mmcblk1p2 --efi-directory=/boot/efi/ --boot-directory=/boot/
   >
   > sudo grub-mkconfig -o /boot/grub/grub.cfg

5. 拷贝驱动固件到/lib/firmware，重启系统，驱动均完美解决

6. 临时解决显示异常modeset=0

### 懒人安装方式

1. 使用Rufus烧录镜像到优盘，选择ISO模式，镜像使用20.04.6 Desktop
2. 拷贝bootia32.efi到优盘/EFI/boot
3. 正常进行系统安装，切记联网安装
4. 拷贝驱动固件到新系统，重启，结束

### 文件说明



