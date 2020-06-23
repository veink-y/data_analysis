
## superset介绍

superset是airbnbd开源的BI面板，基于python框架，集成了Flask、D3、Pandas、SqlAlchemy等框架。

现成虚拟机：

下载链接： [https://share.weiyun.com/51VMwBe](https://share.weiyun.com/51VMwBe)  密码：dahpkn

## 虚拟机centos最小化安装

下载地址：  
[centosmini下载地址](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1810.iso)  
[centos完整版下载地址](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-DVD-1810.iso )

安装过程：

### yum更换源

参考： [https://www.jianshu.com/p/4aa7b63f9026](https://www.jianshu.com/p/4aa7b63f9026)

```shell
#备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#centos7源
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
#生成缓存
yum makecache
```

### Miniconda安装

```shell
#获取安装脚本
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
#安装出错说不能解压的，需要先安装bzip2
bash Miniconda3-latest-Linux-x86_64.sh
#使bashrc生效
source ~/.bashrc
```

### conda更换源

```shell
#清华源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

### pip更换源

暂时修改

```shell
pip install package名字 -i https://pypi.tuna.tsinghua.edu.cn/simple/
```

永久修改

修改 vim ~/.pip/pip.conf 文件，内容如下

```shell
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

## Superset安装

### 虚拟环境的创建

```shell
# 创建superset虚拟环境
conda create -n superset python=3.6
# 激活superset 环境
conda activate superset
# 不需要使用的时候，停用环境
conda deactivate
# conda 删除虚拟环境
conda remove -n py36 --all
```

### 依赖安装

```shell
yum install gcc libffi-devel python-devel openssl-devel
yum install gcc-c++
yum install gcc-c++ python-devel.x86_64 cyrus-sasl-devel.x86_64
```

### pip安装

```shell
pip install superset
```

### 初始化superset

创建管理员用户

```shell
fabmanager create-admin --app superset  
# 此处报错cannot import name '_maybe_box_datetimelike'，安装pandas0.23.4
# ps：对应版本号为superset-0.28.1
```

如图：

初始化数据源

```shell
superset db upgrade   #此处失败则无法显示superset看板界面

superset load_examples 

superset init
```

### 错误处理

1.superset-0.28.1版本支持的pandas支持版本为0.23.4

```shell
pip uninstall pandas
pip install pandas==0.23.4
```

2.superset db upgrade失败的话，需要降级sqlalchemy

```shell
pip uninstall sqlalchemy
pip install sqlalchemy==1.2
superset db upgrade
```

## superset使用

### 运行superset

```shell
superset runserver
```

默认端口为8088，在runserver后面添加 -p XXXX 可更改为其他端口

在浏览器输入 [http://虚拟机ip:8088](http://虚拟机ip:8088)  ，即可打开界面

如果无法打开，需要打开端口，或者关闭centos防火墙

```shell
#关闭防火墙
systemctl stop firewalld

#开永久端口号 
firewall-cmd --add-port=8088/tcp --permanent
# 提示    success 表示成功

#查看开启的端口号
firewall-cmd --zone=public --list-ports
```

### 连接mysql数据库

```shell
# 安装mysqlclient
pip install mysqlclient
# 安装pymysql
conda install pymysql
```

连接mysql数据库可能会 遇到不少报错，可以网上搜索解决办法

主要解决办法是需要在mysql配置文件配置跳过ssl和grant等，并打开mysql的外部访问，如端口和外部可用账户

### 连接Hive数据库

```shell
#安装pyhive
conda install pyhive
```

需要hive服务端开启hiveserver或hiveserver2

