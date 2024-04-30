# Centos7.9安装python3.10  


## 先升级openssl  

* 查看当前openssl的版本信息
```
[root@hadoop100 ~]# openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
```  
需要升级

REF: https://github.com/stardock/openlssl3.2  

```
虚拟机默认实验sudo yum -y python3是会报错的，因为的库没有。所以我们可以安装epel扩展软件包
sudo yum -y install epel-release 本文用编译安装所以这里用不上
```  

## 安装依赖包

`yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel libffi-devel gdbm-devel db4-devel libpcap-devel xz-devel make`  

## 下载python3.10  

[rootg@hadoop100 ~]$ wget https://www.python.org/ftp/python/3.10.14/Python-3.10.14.tgz
[rootg@hadoop100 ~]$ tar -xvzf Python-3.10.14.tgz
[rootg@hadoop100 ~]$ cd Python-3.10.14/

## 设置调用openssl3.2  

1）：在Python3.7之后的版本，依赖的openssl，必须要是1.1或者1.0.2之后的版本，或者安装了2.6.4之后的libressl，linux自带的openssl版本过低。  
2）：在编译之后修改Modules/Setup文件中的部分内容，打开ssl，或者在编译的时候指定–with-ssl参数（我使用的是前面的方式，后一种方式的真实性有待考究）  

* 修改 vim ../Python-3.10.14/Modules/Setup  

shift+g 跳转到末尾 然后在末尾粘贴如下内容,wq保存退出  
```
_socket socketmodule.c
# Socket module helper for SSL support; you must comment out the other
# socket line above, and possibly edit the SSL variable:
SSL=/usr/local/openssl3.2
_ssl _ssl.c \
        -DUSE_SSL -I$(SSL)/include -I$(SSL)/include/openssl \
        -L$(SSL)/lib -lssl -lcrypto
```  

# 安装Python3.10  

`cd Python-3.10.14`  
* 指定编译语言(可选)
```
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN.UTF-8
```
* 指定安装目录  
`./configure --prefix=/usr/local/python3.10 --with-ssl=/usr/local/openssl3.2`  
或（主要看/usr/local/openssl是否存在）
* 编译安装  
```
make
make install
make clean
```  

* 安装完python后，切换到安装的bin目录，检查ssl功能是否正常
```
cd /usr/local/python3.10/bin
./python3
import ssl
exit()   #退出python3解释器后执行查看./pip3 list
./pip3 list
```

## 将 python 进行软连接，将 python 和 pip 设置全局变量  
```
ln -s /usr/local/python3.10/bin/python3.10 /usr/bin/python3.10
ln -s /usr/local/python3.10/bin/pip3.10 /usr/bin/pip3.10
ln -s /usr/local/python3.10/bin/python3.10 /usr/bin/python3
ln -s /usr/local/python3.10/bin/pip3.10 /usr/bin/pip3
```

## 配置环境变量  

* 编辑/etc/profile，在尾部添加如下代码：  
`echo 'export PATH=$PATH:/usr/local/python3.10/bin' >> /etc/profile`  

* 使用source命令重新加载/etc/profile  
`source /etc/profile`  

* 查看安装的python版本  
`python3 --version`

REF: https://www.jianshu.com/p/fd3aa300bbcf
