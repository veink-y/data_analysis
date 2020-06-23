
--2019年10 月 7 日，决定不再多折腾 WSL，转向黑苹果进行 python 数据分析--



## WSL简介

WSL>>>Windows Subsystem for Linux



## WSL安装

### 安装前置工作

在windows 功能中启用，位置如下

控制面板>>>程序>>>启动或关闭Windows功能>>>适用于Linux的Windows子系统

### 安装方式

目前WSL 安装方法两种：

1、 通过 Microsoft Store 安装

2、 通过 lxrunoffice 安装

## WSL的python玩法

1、通过pycharm远程连接WSL直接运行python代码，感觉比windows本身快那么一丢丢（心理因素）

​		ps: 最近 pycharm2019.2 版本的 SSH 有点小毛病，会下载根目录所有的文件到 C 盘的 pycharm 配置文件位置

2、 通过vscode的remote-WSL连接，和pycharm同理

​		ps：需要重新安装插件	

3、 通过jupyter使用WSL上的python

​		ps：这是我最常用的，比 windows 本身的 powershell 方便好玩

## WSL的ln命令

WSL中，windows本身目录挂载在 /mnt 下，无需额外再挂载，但是大部分时候并不太方便

```bash
ln -s /mnt/d/jupyter jupyter
```

会将 jupyter 做一个 当前目录的软链接，挺好用的一种方式。



