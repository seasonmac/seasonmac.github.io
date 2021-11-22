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
``` shell
diff --git a/core/res/res/values/config.xml b/core/res/res/values/config.xml
index d1b3fec..47ef4f1 100644
--- a/core/res/res/values/config.xml
+++ b/core/res/res/values/config.xml
@@ -1719,7 +1719,7 @@
     <bool name="config_actionMenuItemAllCaps">true</bool>

     <!-- Remote server that can provide NTP responses. -->
-    <string translatable="false" name="config_ntpServer">time.android.com</string>
+    <string translatable="false" name="config_ntpServer">pool.ntp.org</string>
     <!-- Normal polling frequency in milliseconds -->
     <integer name="config_ntpPollingInterval">86400000</integer>
     <!-- Try-again polling interval in milliseconds, in case the network request failed -->
```

### 定制网络连接监控服务器地址
- 在**frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java**文件中，将谷歌的服务器地址修改为国内的地址
``` shell
diff --git a/services/core/java/com/android/server/connectivity/NetworkMonitor.java b/services/core/java/com/android/server/connectivity/NetworkMonitor.java
index 0ee2a41..ca5fd11 100644
--- a/services/core/java/com/android/server/connectivity/NetworkMonitor.java
+++ b/services/core/java/com/android/server/connectivity/NetworkMonitor.java
@@ -87,12 +87,12 @@ public class NetworkMonitor extends StateMachine {
     // Default configuration values for captive portal detection probes.
     // TODO: append a random length parameter to the default HTTPS url.
     // TODO: randomize browser version ids in the default User-Agent String.
-    private static final String DEFAULT_HTTPS_URL     = "https://www.google.com/generate_204";
+    private static final String DEFAULT_HTTPS_URL     = "https://connect.rom.miui.com/generate_204";
     private static final String DEFAULT_HTTP_URL      =
-            "http://connectivitycheck.gstatic.com/generate_204";
-    private static final String DEFAULT_FALLBACK_URL  = "http://www.google.com/gen_204";
+            "http://connect.rom.miui.com/generate_204";
+    private static final String DEFAULT_FALLBACK_URL  = "http://www.baidu.com";
     private static final String DEFAULT_OTHER_FALLBACK_URLS =
-            "http://play.googleapis.com/generate_204";
+            "http://www.baidu.com";
     private static final String DEFAULT_USER_AGENT    = "Mozilla/5.0 (X11; Linux x86_64) "
                                                       + "AppleWebKit/537.36 (KHTML, like Gecko) "
                                                       + "Chrome/52.0.2743.82 Safari/537.36";
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
