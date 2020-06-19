## 上传本地文件到vps：

```
#上传文件
scp  本地文件 root@ip:vps目录
#eg： 
scp /Users/vein/Downloads/DataFrog/import_data_to_hive.sql root@101.132.108.116:/root/adventure_demon/


#上传文件夹
scp -r 本地文件夹 root@ip:vps目录
#eg：
scp -r /Users/vein/OneDrive/jupyter/adventure3/adventure_new_noheadr/ root@101.132.108.116:/root/adventure_demon/
```



## pip更换源

```shell
cd $HOME  
mkdir .pip  
cd .pip
sudo vim pip.conf  

在里面添加  
[global]  
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn 
disable-pip-version-check = true  
timeout = 6000
```



## ubuntu常用命令

```shell
sudo apt-get update  更新源
sudo apt-get install package 安装包
sudo apt-get remove package 删除包
sudo apt-cache search package 搜索软件包
sudo apt-cache show package  获取包的相关信息，如说明、大小、版本等
sudo apt-get install package --reinstall  重新安装包
sudo apt-get -f install  修复安装
sudo apt-get remove package --purge 删除包，包括配置文件等
sudo apt-get build-dep package 安装相关的编译环境
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-cache depends package 了解使用该包依赖那些包
sudo apt-cache rdepends package 查看该包被哪些包依赖
sudo apt-get source package  下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理无用的包
sudo apt-get check 检查是否有损坏的依赖
```



## mac更换计算机名

```shell
sudo scutil --set HostName newName 
# newName 为新名字
```



### conda 不显示 base

```shell
changeps1:False
```

