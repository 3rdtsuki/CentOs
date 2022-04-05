VMWare12密钥：5A02H-AU243-TZJ49-GTC7K-3C61N

#### 常用操作

设置root密码`sudo passwd root`

在当前目录查找某个字符串 `grep str *`，在当前目录下递归查找字符串`grep -r str ./`

新建文档 `touch filename`

编辑文档 `nano/vim/gedit filename`

重命名或移动 `mv oldpath newpath`

删除目录 `rm -rf dir_path` 

编译 `g++ test.cpp -o test`

目录生成树状图 `tree`



#### WSL环境下安装Ubuntu

https://www.computerhope.com/issues/ch001879.htm#install
WSL环境下安装，不用装虚拟机



#### Ubuntu18没有上网图标

```bash
sudo service network-manager stop
sudo rm /var/lib/NetworkManager/NetworkManager.state
sudo service network-manager start

sudo nano /etc/NetworkManager/NetworkManager.conf
把false改成true
```


#### 虚拟机间连接

##### 不同主机上两台Ubuntu虚拟机之间如何ping通

[不同的虚拟机之间怎么保证能互相ping通-百度经验 (baidu.com)](https://jingyan.baidu.com/article/29697b91c04e10ea20de3c9e.html)，虚拟机设置中网络适配器修改为桥接模式，修改全局LAN区段为主机所在子网区段，进入Ubuntu后`ifconfig`可发现IP已经变为主机网段。之后直接`ping ip`即可

`/etc/hosts`中保存了每个IP和对应的主机名

`/etc/hostname`修改主机名

##### ssh连接

首先，两台机器上都要安装openssh-server，`sudo apt-get install openssh-server`。并且双方可以互ping

然后在主节点上ssh对方`ssh user@hostname`，输入yes，输密码，即可远程控制对方的shell。

##### scp：基于ssh传输目录

`scp -r 本地path user@hostname:目的path  `

例如`scp -r ./hadoop user@hostname:~/Downloads/hadoop`
