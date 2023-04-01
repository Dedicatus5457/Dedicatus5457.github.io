---
title: archlinux-安装备忘录
date: 2023-03-04 20:21:07
tags:
---
**在我给我的联想G470安装arch时碰到的问题解决备忘录**
## 浏览器/wine/系统全局无法显示中文字体
这大多是中文字体不全导致的，一般使用Pacman安装开源几个中文字体就可以解决，你也可以用其他更花里胡哨的字体。
```
Pacman -S wqy-zenhei adobe-source-han-serif-cn-fonts # 其实只安装第一个已经可以解决大部分中文显示方块/纯白的问题
```
注销或者重启后生效。

如果无效，那请检查您的local.gen文件是否注释掉了 `zh_CN.UTF-8 UTF-8`
```
vim /etc/locale.gen # 当然也可以使用其他编辑器如:vi nano emacs等等
```
往下翻找到 `zh_CN.UTF-8 UTF-8` 行，如果您使用vim/nvim，那可以在Normal-mode下输入/zh_CN.UTF-8 UTF-8更快的找到此字符串的所在位置。然后删除`zh_CN.UTF-8 UTF-8`前面的#符号取消注释并保存退出此文件

回到终端(tty)
输入
```
locale-gen  #重新生成locale并生效
```
然后重复上面的操作使用pacman安装中文字体，如查找不到此包，请输入 `pacman -Syu` 更新并同步本地pacman软件包库。

## 安装archlinuxcn软件源内的软件包安装无法校验PGP签名公钥
因为您本地并没有archlinuxcn源的密钥环，所以无法校验软件包PGP签名故无法安装，
```
 pacman报错:==> 错误： 一个或多个 PGP 签名无法校验！
==> 错误：Makepkg 无法构建 xxxxxx 
```

只需要使用pacman安装archlinuxcn的密钥环即可
```
pacman -S archlinuxcn-keyring
```
当然有个智将办法，那就是编辑/etc/pacman.conf，找到SigLevel那行，注释掉或者改成
`SigLevel = Never` 

不过这种方法非常愚蠢，~~相当于你懒得拿钥匙开门然后把锁给拆了，方便倒是方便了~~.
## 让笔记本合上盖子但是不自动挂起休眠
编辑/etc/systemd/login.conf
```
vim /etc/systemd/login.conf
```
找到HandleLidSwitch行，编辑#HandleLidSwitch=suspend为` HandleLidSwitch=ignore `

HandleLidSwitch定义笔记本盖上盖子后触发的操作，默认=suspend，suspend的意思在这里可以理解为windows中的休眠，ignore则是不做任何操作

编辑并保存后，重启systemd-logind
```
systemctl restart systemd-logind
```
or

```
service systemd-logind restart
```



