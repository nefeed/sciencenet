# 个人服务器搭建飞机场Shadowsocks
> 鉴于目前中美关系紧张，“五眼”联盟为了能够上Twitter声援我们的外交宣传事业，所以有志之士都需要能够“科学上网”，在境外主流媒体上留下自己的誓词。
Hua Chunying 华春莹：@SpokespersonCHN
Lijian Zhao 赵立坚：@zlj517

## 登录个人服务器
首先需要获取一台境外服务器，并确定服务器的公网ip或域名，以及ssh的端口和可用root权限账号和密码，Win使用Xshell进行登录，windows版下载地址：
[国外云盘1下载](https://tr61.free4444.xyz/Xshell_setup_wm.exe) [国外云盘2下载](https://tr71.free4444.xyz/Xshell_setup_wm.exe)
Mac使用Terminal终端或第三方终端工具，如iTerm2工具，并输入指令进行登录（```ssh -p {{$端口号}} root@{{$ip地址或域名}}```），例如如下指令：
```shell
ssh -p 22 root@192.168.0.1
```
## 关闭防火墙
因为我们需要开放TCP端口供用户端设备进行代理实现“科学上网”，所以需要对SS(shadowsocks)开放的端口进行放行，那么最快的放行端口方法即是全部放行（关闭防火墙）。
```shell
systemctl stop firewalld.service
```
关闭后可以执行执行查询关闭状态。
```shell
systemctl status firewalld.service
```
如图显示inactive(dead)即表示关闭成功。
![image.png](https://upload-images.jianshu.io/upload_images/875437-3587ebe691171ed2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 安装Python python-setuptools及Pip软件管理工具
> 以下教程的演示环境为CentOS 7.5，因目前主流服务器均为CentOS，所以其他环境的同学可以转战其余教程，相信庞大的互联网“图书馆”一定有你们同样环境的教程。


首先安装python-setuptools，以实现安装第三方Python包Pip
```shell
yum install python-setuptools
```
然后安装Pip
```shell
easy_install pip
```
使用Pip安装飞机场shadowsocks
```shell
pip install shadowsocks
```
当日志内容如下时，说明安装已经成功：
```shell
[root@localhost ~]# pip install shadowsocks
DEPRECATION: Python 2.7 will reach the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 won't be maintained after that date. A future version of pip will drop support for Python 2.7. More details about Python 2 support in pip, can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support
Collecting shadowsocks
  Downloading https://files.pythonhosted.org/packages/02/1e/e3a5135255d06813aca6631da31768d44f63692480af3a1621818008eb4a/shadowsocks-2.8.2.tar.gz
Installing collected packages: shadowsocks
  Running setup.py install for shadowsocks ... done
Successfully installed shadowsocks-2.8.2
```
## shadowsocks服务器配置
在/etc文件下创建shadowsocks.json配置文件。即通过如下命令：
```shell
sudo vi /etc/shadowsocks.json
```
在其中写入如下配置：
```shell
{
  "server": "0.0.0.0",
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "port_password": {
    "8888": "password"
  },
  "timeout": 600,
  "method": "aes-256-cfb"
}
```
其中参数解析如下：
- server 表示监听的地址，这里表示监听所有的地址；
- local_address：本地地址
- local_port：本地端口
- port_password：监听外部请求的端口和对应的密码；此处可以配置多个，以逗号分隔。
- timeout：超时时间600秒。
- method：通信加密方式。
启动或停止服务
- 启动命令：
```shell
sudo ssserver -c /etc/shadowsocks.json -d start
```
- 停止命令：
```shell
sudo ssserver -c /etc/shadowsocks.json -d stop
```
命令拓展，可通过执行ssserver获得更多的参数支持。
```shell
[root@localhost ~]# ssserver -help
option -e not recognized
usage: ssserver [OPTION]...
A fast tunnel proxy that helps you bypass firewalls.
You can supply configurations via either config file or command line arguments.
Proxy options:
  -c CONFIG              path to config file
  -s SERVER_ADDR         server address, default: 0.0.0.0
  -p SERVER_PORT         server port, default: 8388
  -k PASSWORD            password
  -m METHOD              encryption method, default: aes-256-cfb
  -t TIMEOUT             timeout in seconds, default: 300
  --fast-open            use TCP_FASTOPEN, requires Linux 3.7+
  --workers WORKERS      number of workers, available on Unix/Linux
  --forbidden-ip IPLIST  comma seperated IP list forbidden to connect
  --manager-address ADDR optional server manager UDP address, see wiki
General options:
  -h, --help             show this help message and exit
  -d start/stop/restart  daemon mode
  --pid-file PID_FILE    pid file for daemon mode
  --log-file LOG_FILE    log file for daemon mode
  --user USER            username to run as
  -v, -vv                verbose mode
  -q, -qq                quiet mode, only show warnings/errors
  --version              show version information
Online help: <https://github.com/shadowsocks/shadowsocks>
```

## 使用客户端进行代理实现“科学上网”
IOS设备需要切换App Store到海外账号，然后安装SS工具，如免费的Sockswitch；Android用户需要安装Shadowsocks纸飞机；路由器用户可以安装SS插件实现连上路由器即“科学上网”。
![Android演示](https://upload-images.jianshu.io/upload_images/875437-a163320454024f8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![KoolShare演示](https://upload-images.jianshu.io/upload_images/875437-27c64a8034369872.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
因为是境外上网，DNS建议设置为Google(```8.8.8.8```)。

