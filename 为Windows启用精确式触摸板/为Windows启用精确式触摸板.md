# 为Windows启用精确式触摸板
> 导读：Windows上的触摸板体验一直为大家所诟病，各家厂商触摸板质量参差不齐，体验完全没有Mac上的好。不过好消息是微软有提供一个叫“精准式触摸板”的东西，将其安装作为触摸板驱动底层后，便可由Windows本身来接管触摸板功能，提供更好的交互体验。

```
系统版本：Windows 10(1803)
触摸板类型：Synaptics SMBus ClickPad
```

## 前言
- 本教程只适合应用于Synaptics厂商的触摸板，如果你的触摸板厂商为Elan，请参考这篇专栏：[觉得 Windows 笔记本的触控板不好用？你可以试试装个新驱动 - 少数派的文章 - 知乎](https://zhuanlan.zhihu.com/p/38249316)。查看厂商的方法为：
  ![查看你的触摸板厂商](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/查看你的触摸板厂商.png)
- 安装前，请确保你有一个可用的USB/无线鼠标设备，因为在安装过程中触摸板会失效一段时间。

## 安装步骤

- 首先，[点击这里](https://download.lenovo.com/pccbbs/mobiles/n1mgx14w.zip) 下载Synaptics触摸板对应的驱动，然后将其解压到目录中。

- 打开 `设备管理器` ，然后定位到你的触摸板，`右键单击 → 卸载设备` :
![卸载触摸板设备及驱动程序](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/卸载触摸板设备及驱动程序.png)

- 勾选： `删除此设备的驱动程序软件` ，然后点卸载。如此做的话，Windows便会恢复到使用最初始的触摸板驱动程序。卸载完成后重启你的电脑：
![勾选删除设备驱动程序软件](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/勾选删除设备驱动程序软件.png)

- 重启完后再次打开 `设备管理器` ，定位到你的触摸板，`右键单击 → 更新驱动程序` ：
![卸载完成后更新驱动程序](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/卸载完成后更新驱动程序.png)

- 点击 `浏览我的计算机以查找驱动程序软件` ：
![浏览我的计算机以查找驱动程序软件](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/浏览我的计算机以查找驱动程序软件.png)

- 在下图红色框内点击 `浏览` ，然后定位到你前面下载解压得到的驱动程序目录内。接着点击下方的 `让我从计算机上的可用驱动程序列表中选取` ：
![定位到第一步中下载的驱动程序](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/定位到第一步中下载的驱动程序.png)

- 选中你的触摸板，然后点击下方的 `从磁盘安装` 按钮：
![点击从磁盘安装按钮](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/点击从磁盘安装按钮.png)

- 接着，在新弹出来的框中点击 `浏览` ，然后定位到驱动解压目录中的 `Autorun.inf` ，点击 `打开` ：
![打开Autorun脚本](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/打开Autorun脚本.png)

-  点击 `确定` ：
![确定从磁盘安装](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/确定从磁盘安装.png)

- 选中图中的 `Synaptics Pointing Device` ，单击下一步：
![选中SynapticsPointingDevice](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/选中SynapticsPointingDevice.png)

- 出现 `更新驱动程序警告` 窗口时，点击 `是` ，接着便会安装驱动程序：
![忽略更新驱动程序警告](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/忽略更新驱动程序警告.png)

- 安装完成后重启你的电脑。

- 重启完后，`点击Windows徽标 → 设置 → 触摸板` ，你会发现精确式触摸板已经被安装了，不过此时触摸板并不能使用：
![精确式触摸板已经被安装](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/精确式触摸板已经被安装.png)

- 再次打开 `设备管理器` ，定位到你的触摸板，`右键单击 → 更新驱动程序` ：
![再次更新驱动程序](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/再次更新驱动程序.png)

- 这次选择 `自动搜索更新的驱动程序软件` ：
![选择自动搜索更新的驱动程序软件](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/选择自动搜索更新的驱动程序软件.png)

- Windows将会自动搜索下载安装驱动程序，之后，按照要求重启电脑即可。

- 接下来便可享受精确式触摸板带来的全新体验了：
![享受精确式触摸板带来的体验吧](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/images/享受精确式触摸板带来的体验吧.png)

## 驱动下载
如果前面的驱动程序下载地址失效了，试试 [从Github上下载驱动程序](https://github.com/enify/blog/blob/master/为Windows启用精确式触摸板/resources/n1mgx14w.zip)

## 参考源
  - [为Synaptics与Elan驱动的触控板安装微软精确式触控板驱动](https://zhuanlan.zhihu.com/p/28888470)
  - [觉得 Windows 笔记本的触控板不好用？你可以试试装个新驱动](https://zhuanlan.zhihu.com/p/38249316)
  - [Enable precision touchpad on HP Spectre x360 (2017) and other devices. : Windows10](https://www.reddit.com/r/Windows10/comments/6qz30h/enable_precision_touchpad_on_hp_spectre_x360_2017/)

