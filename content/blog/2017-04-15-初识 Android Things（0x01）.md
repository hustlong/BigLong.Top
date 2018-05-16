---
title: 初识 Android Things（0x01）
date: 2017-04-15
tags:
    - "物联网"
    - "Android"
categories:
    - "技术文章"
---


谷歌推出的 Things 物联网操作系统也有小半年时间了，刚刚推出的时候就开始了解相关资料，跃跃欲试，不过工作原因一直没来得及，现在转眼已是小半年时间了，现在出发也不晚，老司机，走起～<!--more-->


# Android Things 是什么

>Build connected devices for a wide variety of consumer, retail, and industrial applications


![Android Things](http://upload-images.jianshu.io/upload_images/1815913-fa667add2486585e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2016年12月，谷歌发布了物联网操作系统 Android Things，Things 可以说是 Android 系统的一个分支。结合谷歌为物联网设备推出的通讯协议Weave，Android Things将在谷歌物联网战略中发挥重要作用。

Things 的前身是谷歌于2015年发布的物联网底层操作系统 Brillo，Brillo 源于Android，是对Android底层的一个细化，得到了Android的全部支持，比如蓝牙、Wifi等技术，并且能耗很低，安全性很高，任何设备制造商都可以直接使用，是一个完全端到端的应用。

不同的是，Brillo把C++作为主要开发环境，它的开发和部署明显不同于常规Android开发，因此这对于 Android 开发者并不友好，而Android Things则面向所有Java开发者，不管开发者有没有移动开发经验，有经验更容易上手，Android Things系统支持原始Android SDK中的一个子集，开发环境就用 Android Studio，后面再详细演示第一个Android Things 工程。

# Things的优势

## The ease and power of Android

>If you can build an app, you can build a device

*  **The Android ecosystem**
Leverage existing Android development tools, APIs, resources, and a thriving developer community.

* **New APIs for IoT devices**
Develop with new Android framework APIs that provide low level I/O and libraries for common components like temperature sensors, display controllers, and more.
* **Trusted security**
Take advantage of regular best-in-class security updates by building on top of the Android OS.

## Rapid prototypes to real products

>Anyone, from startups to large companies, can build commercial products at scale

* **Turnkey hardware solutions**
Our certified development boards include the System on Chip (SoC), RAM, wireless radios, and more to get you started quickly.

* **Low barrier to entry**
Start prototyping immediately with supported boards and a developer build of Android Things. There's no need for kernel, firmware, or board development.

* **Build products at scale**
Shipping products is easy because our prototype boards are commercial grade. The same boards can be easily customized by your prototype supplier to fit your specific form-factor and other needs, all while running the same software.

## The power of Google at your fingertips

>Build and manage millions of devices at Google’s scale

* **Google APIs and services**
Leverage a wide range of Google services, such as Google Play services and Weave.

* **System updates and support**
We provide the system image, updates, fixes, and more, so you can focus on creating compelling products.

* **Large scale infrastructure**
Push Google-provided OS updates and your own app updates, using the same OTA infrastructure we use for our own products.

# 硬件选型

工欲善其事，必先利其器。学习Android Things，无需要求你精通嵌入式系统设计等相关知识，但也要稍微懂一点硬件底层相关的东西。当前谷歌官方推荐适配了几款硬件开发板，只需要下载固件安装就可以着手开发了，很简单有木有！选型硬件要根据自己的开发需求来选择，当然普通学习者也要考虑手里的软妹币，有款板子挺贵的。。。

![Developer Kits](http://upload-images.jianshu.io/upload_images/1815913-42e0ccc068526b2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当前谷歌的 Things 官网上已经支持了以上五块板子，第五个树莓派3一下子就吸引住了我滴目光，嘿嘿嘿，便宜又好用，果断就买它了！

官网还推荐了一些学习用到的外围配件，如下图：


![Featured peripherals](http://upload-images.jianshu.io/upload_images/1815913-eed0e7df24a7467e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为工作室里有很多电子元器件配件等等，所以我就只买了一块树莓派3B，小风扇、散热片、亚克力外壳，16G TF存储卡，读卡器，加在一起不超过350RMB。又要吃土了。。。

# 烧写固件

收到快递的心情是愉快的，三下五除二拆开包裹，依次点清零件，先来一张全家福：


![树莓派3B 组装前](http://upload-images.jianshu.io/upload_images/1815913-3b3f2fd87129de11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接下来就是组装了，其实树莓派不用散热也是可以工作的，那我为什么还要买这鬼东西，嘿嘿嘿，组装好之后比较帅气吧！组装其是是小事情了，曾经组装过桌面级FDM 3D 打印机，超散的零件，small case！


![树莓派3B](http://upload-images.jianshu.io/upload_images/1815913-2c8c30e9a57a77a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当当当～如此帅气的开发板，这点随我！

* **第一步**

接下来是从官网***下载固件***，[下载地址](https://developer.android.com/things/preview/download.html)
，压缩包大概200多兆，不过解压有接近4个G！下载完之后可以不用解压！
下载***烧写软件 Etcher.app*** ，这个是Mac端，其他平台请按需下载。这个软件貌似得Fan墙之后才能下载。。。如果有下载不下来的，可以发邮件问我要，我看到会尽快回复（邮箱 biglongcc@gmail.com）。

* **第二步**

打开Etcher.app --> 装载固件 --> 选择烧写tf卡 --> 开始烧写。如下图所示：

![烧写](http://upload-images.jianshu.io/upload_images/1815913-ad4141e7d813eb8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **第三步**

如下图所示，1处供电，电源为5V／2A，3处连接HDMI到显示器，2处接一根网线到路由器LAN口。
![连接线](http://upload-images.jianshu.io/upload_images/1815913-a19f25876061bff4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **第四步**

静静等待开机～～开机结束后，iot-launcher 会显示IP地址

![等待开机](http://upload-images.jianshu.io/upload_images/1815913-1377ab5a45e6f64c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **第五步**

用adb连接树莓派。

```java
$ adb connect <ip-address>
connected to <ip-address>:5555
```

* **第六步**

连接Wi-Fi。wifi 连接成功就可以把网线去掉了！

```java
$ adb shell am startservice \
    -n com.google.wifisetup/.WifiSetupService \
    -a WifiSetupService.Connect \
    -e ssid <Network_SSID> \
    -e passphrase <Network_Passcode>
```

* **第七步**

可以通过串口进行调试，连接6、8、10三个引脚到串口转USB模块。串口配置以下参数：

```java
Baud Rate: 115200
Data Bits: 8
Parity: None
Stop Bits: 1
```

![pinout-raspberrypi3](http://upload-images.jianshu.io/upload_images/1815913-c915398359e50921.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# The End

好了，基本就是这样了，下一节来点亮一个LED灯。文章里面的英语部分是直接从Google官网整理过来的，由于英语水平有限就没有翻译了，哈哈哈，***“是外语啊，可真得好好学”***，否则很多优秀的资料都看不了多可惜！


![人民的名义-截图](http://upload-images.jianshu.io/upload_images/1815913-e61f23dd656c1f5f.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
