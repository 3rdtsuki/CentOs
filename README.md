VMWare12密钥：5A02H-AU243-TZJ49-GTC7K-3C61N

#### 常用操作

设置root密码`sudo passwd root`

在当前目录查找某个字符串 `grep str *`，在当前目录下递归查找字符串`grep -r str ./`

新建文档 `touch filename`

编辑文档 `nano/vim/gedit filename`

重命名或移动 `mv oldpath newpath`

删除 `rm -rf `

编译 `g++ test.cpp -o test`

目录生成树状图 `tree`

#### WSL环境下安装Ubuntu

https://www.computerhope.com/issues/ch001879.htm#install
WSL环境下安装，不用装虚拟机

#### Ubuntu18没有上网图标

```bash
service network-manager stop
rm /var/lib/NetworkManager/NetworkManager.state
service network-manager start

nano /etc/NetworkManager/NetworkManager.conf
```
把false改成true
