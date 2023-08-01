## 配置环境和编译链
### 安装依赖
```
sudo apt-get install libncurses5-dev libncurses-dev libssl-dev device-tree-compiler bc cpio lib32ncurses5-dev lib32z1 build-essential binutils bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev git
```

### 安装编译工具链
```
git clone https://github.com/LineageOS/android_prebuilts_gcc_linux-x86_aarch64_aarch64-linux-android-4.9 aarch64-linux-android-4.9 --depth=1
git clone https://github.com/LineageOS/android_prebuilts_gcc_linux-x86_arm_arm-linux-androideabi-4.9 arm-linux-androideabi-4.9 --depth=1
wget https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/+archive/refs/heads/android12-release/clang-r383902.tar.gz ; 
tar -xvf clang*.tar.gz
```

### 下载内核源码
```
git clone https://github.com/dstmath/android_kernel_4.14_oppo_mtk5g.git -b fix-build

git clone https://github.com/dstmath/android_kernel_modules_oppo_mtk5g.git

```

oppo官方开源的源码，各种编译报错，`fix-build`分支是修复之后的可以直接编译的版本。

### 组织目录结构
将`android_kernel_modules_oppo_mtk5g`里面的vendor目录复制出来和`android_kernel_4.14_oppo_mtk5g`文件夹放在同一级下面，目录结构如下：
```
├── android_kernel_4.14_oppo_mtk5g
├── android_kernel_modules_oppo_mtk5g
└── vendor

```

### 编译

```
# 进入内核源码目录
cd android_kernel_4.14_oppo_mtk5g

# 编译工具链的路径需要根据实际情况修改！！！
export  BUILD_CROSS_COMPILE=../../aarch64-linux-android-4.9/bin/aarch64-linux-android-
export  CROSS_COMPILE=../../aarch64-linux-android-4.9/bin/aarch64-linux-android- 
export  CLANG_PATH=../clang/bin 
export  CROSS_COMPILE_ARM32=../../arm-linux-androideabi-4.9/bin/arm-linux-androideabi- 
export  ARCH=arm64

```

然后提取手机里的/proc/config.gz里的config文件，重命名为xxx_defconfig(xxx为机型相关信息，需root)扔到arch/arm64/configs, 然后执行
```
make xxx_defconfig

make -j16
```

编译后的产物在`arch/arm64/boot`目录下。
### magiskboot生成boot.img

工具地址: `https://github.com/svoboda18/magiskboot.git`

提取手机中的boot.img，然后利用magiskboot解包、替换编译出来的Image,生成新的boot-new.img，刷入手机即可。

```
magiskboot unpack ../boot.img

├── dtb
├── kernel
└── ramdisk.cpio

cp arch/arm64/boot/Image kernel

magiskboot repack ../boot.img boot-new.img

├── boot_my.img
├── dtb
├── kernel
└── ramdisk.cpio

```