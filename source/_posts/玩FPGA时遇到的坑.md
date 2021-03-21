---
title: 玩FPGA时遇到的坑
date: 2020-06-21
category: 技术工具
---

趁着618入了一个FPGA板子，但电子这玩意确实坑多，这篇来记录一下遇到过的坑。

## 串口

板子上有一个串口转USB的芯片，型号是叫CP2102。按照指导安装了驱动，结果插上USB线一直是提示“无法识别USB设备”，弄了好久也没搞定。
最后发现！居然是USB线（准确的说，应该是Type-A和mini-B的转换线）有问题……

### Windows

Windows上需要给CP210x系列装个驱动，卖家提供的或者去官网下Windows10的应该都可以（我折腾太久都忘了现在装的是啥了……），插上后可以在设备管理器的“端口(COM和LPT)”里看到串口对应的COM号。
然后可以安装一个串口调试软件（比如这个专栏安利的[https://zhuanlan.zhihu.com/p/109941792](https://zhuanlan.zhihu.com/p/109941792)），从对应的COM口就可以通信了。

### Linux

Linux上，CP210x从4.0开始就内置了CP210x的驱动了（不是默认开启的，通过`sudo modprobe cp210x`可以开启，但是不手动开启也可以）。插上USB线，通过`lsusb`可以看到这一项：

```
Bus 001 Device 005: ID 10c4:ea60 Silicon Labs CP210x UART Bridge
```

可以安装串口调试工具`picocom`，然后用`sudo picocom -b 115200 /dev/ttyUSB0`连接上串口，用`Ctrl+a Ctrl+q`退出。