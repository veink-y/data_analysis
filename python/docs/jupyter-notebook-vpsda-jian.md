
## 一、Ananconda的安装

```py
#Ananconda的安装
wget https://repo.continuum.io/archive/Anaconda3-5.3.0-Linux-x86_64.sh
#安装出错说不能解压的，需要先安装bzip2
bash Anaconda3-5.3.0-Linux-x86_64.sh
#使bashrc生效
source ~/.bashrc
```

## 二、jupyter的配置

进入python环境

```
#进入python环境
python #在命令行输入
```

获取密码的hash值

```
#获取密码的hash值
from notebook.auth import passwd
passwd()
#此处输入密码，返回hash值，保存好hash值然后退出python环境
exit()
```

创建一个jupyter notebook的配置文件

```
#创建一个jupyter notebook的配置文件
jupyter notebook --generate-config #如果提示不建议root使用，加上参数： --allow-root
#编辑jupyter notebook的配置文件
vim  /root/.jupyter/jupyter_notebook_config.py
#添加密码
jupyter notebook password
```

配置文件需要更改的条目如下：

```
#设置jupyter目录    
c.NotebookApp.notebook_dir = '/root/jupyter' 
#设置本地ip为localhost，如果设置为 ‘*’ ，则你可以通过ip直接访问jupyter服务器，对服务器而言相当危险
c.NotebookApp.ip = '127.0.0.1'
#设置不打开vps的浏览器，因为vps没有浏览器
c.NotebookApp.open_browser = False
#设置启动端口，此处端口和反代端口应该一致
c.NotebookApp.port = 9999
#密码hash值，前面获取的hash值填入这里，不要忘记 u 字符
c.NtebookApp.password = u'sha1:0fb5073640dc:dbffde50e33812cf21e52fa195c8fda23d3cea00'
#设置允许远程登录
c.NotebookApp.allow_remote_access = True
```

## 三、关于NGINX的配置

我的方案是用nginx反代jupyter notebook 并开启SSL  
具体操作如下：  
在BT-panel新建网站，无需数据库和FTP，填写好域名  
关于SSL的配置：我采用的SSL for free 的let's encrypt证书，获取到证书后填写到宝塔的其他证书里。此处参考文章：宝塔 Linux 面板手动获取 Let’s Encrypt 免费证书教程  
在配置文件修改成一下代码：

```
#nginx 宝塔配置文件设置，并开启SSL
server {
    server_name xxx.com ; # 服务器域名和 IP 地址
    listen 80;
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    listen [::]:80;
    charset utf-8;

    location / {
        proxy_hide_header X-Powered-By;

        proxy_redirect off;
        proxy_pass http://127.0.0.1:9999;
        proxy_http_version 1.1;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
    }

    #SSL-START SSL相关配置，请勿删除或修改下一行带注释的404规则
    #error_page 404/404.html;
    #HTTP_TO_HTTPS_START
    if ($server_port !~ 443){
        rewrite ^(/.*)$ https://$host$1 permanent;
    }
    #HTTP_TO_HTTPS_END
    ssl_certificate    /etc/letsencrypt/live/520213.xyz/fullchain.pem;
    ssl_certificate_key    /etc/letsencrypt/live/520213.xyz/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    error_page 497  https://$host$request_uri;

    #SSL-END
}
```

## 四、启动jupyter

让jupyter在后台运行有很多方法，有空再慢慢补充。  
此处介绍一种：screen

```
#创建screen mission
screen -S jupyter
#进入screen mission
screen -r jupyter
#查看jupyter对话
screen -ls
```

### 启动jupyter：

root用户：

```
jupyter notebook --allow-root
```

非root用户：

```
jupyter notebook
```



