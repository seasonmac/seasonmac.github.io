---
layout: post
title: "Build Android 10"
subtitle: 'on Pixel 2 Device'
author: "seasonpplp"
header-style: text
tags:
  - Android
  - ROM
---
## 编译源码

- 在 https://source.android.com/setup/start/build-numbers 查找 QQ1A.200105.002 对应的分支：android-10.0.0_r21，记住分支名
``` shell
mkdir Pixel2
cd Pixel2
repo init -u https://android.googlesource.com/platform/manifest -b android-10.0.0_r21 --depth=1
repo sync -j8
repo start android-10.0.0_r21 --all
```
- 在 https://developers.google.com/android/drivers 上下载google和qcom的Pixel2驱动
- 解压这两个压缩包之后，会获得如下两个脚本,执行这两个脚本
``` shell
./extract-qcom-walleye.sh
./extract-google_devices-walleye.sh
```

- 编译源码
``` shell
source build/envsetup.sh
lunch aosp_walleye-userdebug
make -j24
```

- 编译SDK
``` shell
make -j32 && make dmtracedump -j32 && make etc1tool -j32 && make split-select -j32
make bcc_compat -j32 && make apksigner -j32 && make dx -j32 && make libaapt2_jni -j32
make layoutlib-legacy -j32 && make aapt -j32 && make deployagent -j32
make sdk -j32 && make win_sdk -j32
```

## 镜像烧写
```
编译完后，执行：export ANDROID_PRODUCT_OUT=./out/target/product/walleye
执行：fastboot flashall -w
烧写完成后，执行：fastboot reboot
```

## 烧写TWRP
- 从https://twrp.me/google/googlepixel2.html 上选择American版本
- 跳转到https://dl.twrp.me/walleye/ ，下载twrp-3.3.0-0-walleye.img和
twrp-pixel2-installer-walleye-3.3.0-0.zip
- fastboot boot path/to/twrp.img
- 启动TWRP之后，使用adb push path/to/twrp.zip /sdcard/
- 最后，点击twrp的install按钮，选择sdcard目录下的twrp压缩包，进行安装









