#### 常用操作

设置root密码`sudo passwd root`

修改时区

```sh
tzselect
sudo ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 一键改时间，但不保存
timedatectl set-timezone Asia/Shanghai
```

新建文档 `touch filename`

编辑文档 `nano/vim/gedit filename`

重命名或移动 `mv oldpath newpath`

删除目录 `rm -rf dir_path` 

编译 `g++ test.cpp -o test.exe`

目录生成树状图 `tree`

修改文件权限为用户级`chmod 777 filename`

编辑环境变量`nano /etc/profile`，使生效`source /etc/profile`

目录下查找文件`find ./ -name *target*`

#### 文本

在当前目录下查找某个字符串 `grep str *`

在当前目录下查找字符串`grep -r str ./`

将指定文件中的str1替换为str2

```sh
sed -i 's%str1%str2%g' <file_name>
```



#### vim

vim写文件

```sh
vim test.txt
```

`i`进入输入模式，按下`ESC`退出输入模式

非输入模式下

- `:10`定位到第10行
- `:wq`保存文件并退出
- `dd`删除一整行，`dG`删除此行以下全部
- `:set nu`显示行号
- `:g/str1/s//str2/g`用字符串 str2 替换正文中所有出现的字符串 str1
- `/text`：在文件中查找text



#### 查看端口信息

```sh
netstat -ntulp |grep 80 # 查看80端口信息
```

查看进程端口号信息

```sh
ps -aux | grep geth
mika      16713  4.5  1.8 2518172 153560 pts/0  Sl+  20:01   0:44 geth --datadir ./data console
mika      16983  0.0  0.0  16180  1008 pts/1    S+   20:17   0:00 grep --color=auto geth
```



#### apt换阿里源

[ubuntu镜像_ubuntu下载地址_ubuntu安装教程-阿里巴巴开源镜像站 (aliyun.com)](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.4cfe1b11PHMv2E)

##### ubuntu18源

```sh
sudo vim /etc/apt/sources.list
```

里面清空，换成

```sh
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
 
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
 
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
 
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
 
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

然后

```sh
sudo apt-get update
```



##### Kali换源

```
deb https://mirrors.aliyun.com/kali kali-rolling main non-free contrib
deb-src https://mirrors.aliyun.com/kali kali-rolling main non-free contrib
```

下载并添加签名

```sh
wget archive.kali.org/archive-key.asc
sudo apt-key add archive-key.asc
```



#### 无法上网

##### Ubuntu18没有上网图标

```shell
sudo service network-manager stop
sudo rm /var/lib/NetworkManager/NetworkManager.state
sudo service network-manager start
```

##### Kali ifconfig发现没有eth0网卡

```sh
sudo ifconfig eth0 up
sudo dhclient eth0
```

##### Kali有eth0无法上网

```sh
sudo /etc/init.d/networking restart
```



#### ssh

首先，两台机器上都要安装openssh-server，并保证双方可以互ping

```sh
sudo apt-get install openssh-server
```

在主节点上ssh对方

```sh
ssh mika@192.168.10.129# 不指定端口默认22端口
ssh -p 22 mika@192.168.10.129
```


输入yes，输密码，即可远程控制对方的shell。

```shell
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

如果出现上述文字，清空`~/.ssh/known_hosts`文件，再重新建立ssh连接

##### Kali开启ssh

```sh
systemctl start ssh #启用 SSH 服务
systemctl status ssh #查看服务是否成功启用
```

##### scp：基于ssh传输目录

```sh
scp -r <本地path> user@hostname:<目的path>
scp -r listen.py test@192.168.211.144:~/Desktop
```



#### Kali启动Apache服务

```sh
sudo service apache2 start
```

apache目录为`/var/www/html`，浏览器直接IP访问

php.ini文件位于`/etc/php/7.4/apache2/php.ini`



#### FTP

服务端：

- 设定用户名和密码`sudo useradd ftpuser -m`，`sudo passwd ftpuser `
- 用户表中添加用户名`/etc/vsftpd.chroot_list`
- 编辑配置文件`/etc/vsftpd.conf`，包括文件保存的根目录。`allow_writeable_chroot=YES
  local_root=/home/mika/Desktop/ftp/`

```sh
service vsftpd start|stop|restart|status
```

用户端上传文件到服务端：

```sh
ftp <server_ip> # 连接服务端，输入服务端用户名和密码
ftp> put <file_path> # 上传文件到服务端
```

#### ubuntu终端开启标签模式

```
crtl + shift + T
```

#### apt锁解除

直接删除锁文件即可



#### 虚拟机NAT模式下无法ping通宿主机

虚拟机的网段不能和宿主机相同，否则会冲突导致虚拟机拒绝服务



#### WSL环境下安装Ubuntu

WSL：Windows Subsystem for Linux，不用装VMWare

https://www.computerhope.com/issues/ch001879.htm#install

#### mysql安装

```sh
sudo apt install mysql-server

# root下建立新用户
sudo mysql -uroot -proot
create user mika@localhost identified by '1798';
grant all on *.* to mika@localhost;
flush privileges;

sudo mysql -u mika -p
```

#### open: Text file busy

fuser -v filename，然后kill -9 pid

#### ubuntu绑定IP

```sh
sudo vim /etc/network/interfaces
```

在下面增加

```
auto ens33
iface ens33 inet static
address 192.168.10.168
netmask 255.255.255.0
gateway 192.168.10.2
```

重启即可
