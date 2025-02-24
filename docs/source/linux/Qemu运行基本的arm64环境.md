# Qemu运行基本的arm64 环境

## 0.环境

 前置实验环境为：

* ubuntu20
* linux 内核 5.0
* qemu版本 4.2
* gdb : 9.1



安装环境命令

```shell
sudo apt install net-tools libncurses5-dev libssl-dev build-essential openssl qemu-system-arm libncurses5-dev gcc-aarch64-linux-gnu git bison flex bc vim universal-ctags cscope cmake python3-dev gdb-multiarch openjdk-13-jre trace-cmd kernelshark bpfcc-tools cppcheck docker docker.io libelf-dev gcc-7 xterm curl clang exuberant-ctags python-is-python3

sudo apt build-dep linux-image-generic
```



## 1. 使用qemu运行arm64 linux系统



### 1.1 下载linux 内核 

  下载linux5.10.15内核： https://mirrors.tuna.tsinghua.edu.cn/kernel/v5.x/linux-5.10.15.tar.xz
   解压：

```
tar -Jxf linux-5.10.15.tar.xz
```

 除了手动配置 Linux 内核的选项之外，还可以直接复制 Ubuntu Linux 系统中自带的配置文件。需要 比 linux5.10.15低或则相同的 linux版本config 

```
cp /boot/config-5.4.0-26-generic .config
make olddefconfig
make -j$(nproc)
```

### 1.2 制作Ubuntu 发行版的根文件系统

​	Ubuntu 系统提供的 debootstrap 工具可以帮助我们快速创建指定架构的根文件系统。本实验要求使用 debootstrap 工具来创建基于 Ubuntu Linux 20.04 系统的根文件系统，并且要求能够在 QEMU + ARM 实验平台上正确挂载和引导系统。

#### 1.2.1. 安装 debootstrap 工具：

```
sudo apt-get install binfmt-support qemu qemu-user-static debootstrap
```

#### 1.2.2. 用 debootstrap 工具：

```
mkdir -p myrootfs_arm64
sudo debootstrap --arch=arm64 --foreign buster myrootfs_arm64/ http://mirrors.ustc.edu.cn/debian/
```

* --arch：指定要制作文件系统的处理器体系结构，比如 arm64
* buster：指定 Debian 的版本。buster 是 Debian 10 系统。
* myrootfs_arm64：本地目录，最后制作好的文件系统会在此目录



   执行该命令遇到错误

* > ```
  > sudo debootstrap --arch=arm64 --foreign buster myrootfs_arm64/ http://mirrors.ustc.edu.cn/debian/
  > /usr/sbin/debootstrap: 1609: cannot create /home/nvmeon1p2/linux-5.10.15/myrootfs_arm64/test-dev-null: Permission denied
  > E: Cannot install into target '/home/nvmeon1p2/linux-5.10.15/myrootfs_arm64' mounted with noexec or nodev
  > ```

   错误是说 当前 /home/nvmeon1p2/ 挂载的根文件目录 存在nodev

 	nodev 和 nosuid 是 Linux 中的挂载选项，控制设备文件系统的行为。nodev 禁止在特定挂载点上使用设备唯一标识符（主设备号和次设备号），nosuid 则避免在执行二进制文件时使用 SUID 位，这增加了系统的安全性。

解决方法：

​	查看当前目录挂载的文件系统

```
mount | grep "/home/nvmeon1p2"
/dev/nvme0n1p2 on /home/nvmeon1p2 type ext4 (rw,nosuid,nodev,relatime,x-gvfs-show)
```

​	修改fstab将 /home/nvmeon1p2变成如下格式

![img](file:///C:\Users\mc\AppData\Roaming\Tencent\Users\2961606511\QQ\WinTemp\RichOle\[J}CPJ[7D{AMJF38U{5$K$5.png)

```
sudo vim /etc/fstab
LABEL=nvmeon1p2 /home/nvmeon1p2 auto defaults,nofail,x-gvfs-show  0  0
```



#### 1.2.3 debootstrap 制作第二阶段

​	因为主机跑在 x86 架构上，而我们要制作的文件系统是跑在 ARM64 上，因此可以使用 qemu-aarch64-static 来模拟成 arm64 环境的执行环境。

```shell
sudo cp /usr/bin/qemu-aarch64-static myrootfs_arm64/usr/bin/
#下面使用 debootstrap 命令进行软件包的安装和配置。
chroot myrootfs_arm64/ debootstrap/debootstrap --second-stage
```

​	设置根文件系统用户名和密码

```shell
#使用 chroot 命令切换到刚才制作的根文件系统
sudo chroot myrootfs_arm64/
#为 root 用户设置密码：
passwd root
#安装 net-tools 工具。我们以后会用到 ifconfig 工具。
apt update
apt install net-tools build-essential

```

​	配置网卡支持 DHCP 协议。修改/etc/network/interfaces 文件，增加如下内容。

```shell
auto lo 				# 这一行表示当系统启动时，lo 接口（即 回环接口）将被自动启用和配置。
iface lo inet loopback  #iface 表示这是一个网络接口配置  lo 是接口名称  inet 表示这个接口使用的是 IPv4 地址	loopback 指定该接口为回环地址，即 127.0.0.1。
allow-hotplug enp0s1   #这一行指定 enp0s1 网络接口可以在 热插拔 时自动启用 allow-hotplug 表示当设备（如网络卡）被插入或连接时，系统会自动配置该接口
iface enp0s1 inet dhcp  #表示该接口通过 DHCP 协议动态获取 IP 地址
```



#### 1.2.4 制作 ext4 文件系统

​	(1) 首先使用 dd命令来创建一个 image 文件。bs=1M 表示block 大小，count=2048,表示该 image 大小为 2GB

```shell
dd if=/dev/zero of=myrootfs_arm64.ext4 bs=1M count=2048
```

​	(2) 使用 mkfs.ext4 命令来格式化

```
mkfs.ext4 myrootfs_arm64.ext4
```

​	(3) 挂载 ext4 文件系统并拷贝内容

```shell
mkdir -p tmpfs
sudo mount -t ext4 myrootfs_arm64.ext4 tmpfs/ -o loop
sudo cp -af myrootfs_arm64/* tmpfs/
sudo umount tmpfs
chmod 777 myrootfs_arm64.ext4
```



文件系统就做完了



### 1.3 运行 qemu 

​	编译内核

```
export ARCH=arm64
export CROSS_COMPILE=aarch64-linux-gnu-
make defconfig
make -j$(nproc)
```

​	qemu 运行

```
qemu-system-aarch64 \
  -m 1024 \
  -cpu cortex-a57 \
  -M virt \
  -nographic \
  -smp 4 \
  -kernel arch/arm64/boot/Image \
  -append "noinitrd sched_debug root=/dev/vda rootfstype=ext4 rw crashkernel=256M loglevel=8" \
  -drive if=none,file=myrootfs_arm64.ext4,id=hd0 \
  -device virtio-blk-device,drive=hd0 \
  -fsdev local,id=kmod_dev,path=/home/nvmeon1p2/linux-5.10.15/kmodules,security_model=none \
  -device virtio-9p-pci,fsdev=kmod_dev,mount_tag=kmod_mount

```



### 1.4 主机和 虚拟机共享 目录

​	QEMU 提供了多种文件共享方式，最常用的方式是使用 **virtio-9p** 文件系统，您可以通过以下步骤共享文件：

1. 在宿主机上指定共享目录。

2. 使用 `virtio-9p` 或 `virtio-fs` 在启动 QEMU 时挂载共享目录。

3. 在虚拟机中挂载共享的目录。

   ​

   ​

   使用 `virtio-9p` 设备来共享文件，假设我想共享的目录 为 `/home/nvmeon1p2/linux-5.10.15/kmodules` ，关键点是

```shell
#：指定宿主机上的共享目录路径 /home/nvmeon1p2/linux-5.10.15/kmodules
-fsdev local,id=kmod_dev,path=/home/nvmeon1p2/linux-5.10.15/kmodules,security_model=none
#：将宿主机的共享目录映射到虚拟机中。
-device virtio-9p-pci,fsdev=kmod_dev,mount_tag=kmod_mount
```

​	在进入虚拟环境后还需要手动挂载

```
mount -t 9p -o trans=virtio,kernel_version kmod_mount /mnt/kmodules
```



自动挂载方式：

​	进入虚拟机 修改 /etc/fstab文件, 其中  kmod_mount，是启动时指定的mount_tag ，必须一致

```
kmod_mount  /mnt/kmodules  9p  trans=virtio,kernel_version  0  0
```

![img](file:///C:\Users\mc\Documents\WeChat Files\wxid_r7lv93vdskil22\FileStorage\Temp\1739323046385.jpg)