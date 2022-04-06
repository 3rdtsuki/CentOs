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

修改文件权限为用户级`chmod 777 filename`

编辑环境变量`nano /etc/profile`，使生效`source /etc/profile`



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

