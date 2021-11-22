---
layout: post
title: "定制AOSP编译的ROM为国内版本"
subtitle: 'on Pixel 2 Device'
author: "seasonpplp"
header-style: text
tags:
  - Android
  - ROM
---
## 修改源码
### 定制时区和语言
- 在**build/make/tools/buildinfo.sh**文件中添加时区与语言属性
``` shell
echo "persist.sys.language=zh"
echo "persist.sys.country=CN"
echo "persist.sys.localevar="
echo "persist.sys.timezone=Asia/Shanghai"
echo "ro.product.locale.language=zh"
echo "ro.product.locale.region=CN"
```

### 定制ntp服务器地址
- 在**frameworks/base/core/res/res/values/config.xml**文件中，修改时间同步服务器地址**time.android.com**为**pool.ntp.org**
``` xml
    <string translatable="false" name="config_ntpServer">pool.ntp.org</string>
```

### 定制网络连接监控服务器地址
- 在**frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java**文件中，将谷歌的服务器地址修改为国内的地址
- 下面的代码是Android8的，如果是更高版本，没有这个文件，不用修改
``` java
services/core/java/com/android/server/connectivity/NetworkMonitor.java:
    private static final String DEFAULT_HTTPS_URL     = "https://connect.rom.miui.com/generate_204";
    private static final String DEFAULT_HTTP_URL      =
            "http://connect.rom.miui.com/generate_204";
    private static final String DEFAULT_FALLBACK_URL  = "http://www.baidu.com";
    private static final String DEFAULT_OTHER_FALLBACK_URLS =
            "http://play.googleapis.com/generate_204";
            "http://www.baidu.com";
```

## 【tips】同版本系统，更新少量文件文件
``` shell
/opt/android-8.0.0_r21 $ adb  disable-verity
/opt/android-8.0.0_r21 $ adb reboot
/opt/android-8.0.0_r21 $ adb root
/opt/android-8.0.0_r21 $ adb remount
remount succeeded
/opt/android-8.0.0_r21 $ adb sync
./out/target/product/walleye/system/: 4 files pushed. 2283 files skipped. 1.0 MB/s (197486 bytes in 0.195s)
./out/target/product/walleye/vendor/: 502 files pushed. 7.7 MB/s (21884326 bytes in 2.706s)
./out/target/product/walleye/data/: 10 files pushed. 14.3 MB/s (9534184 bytes in 0.634s)
/opt/android-8.0.0_r21 $ adb reboot
```
