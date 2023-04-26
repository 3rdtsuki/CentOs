#### gdb

gdb <程序名> 进入调试

调试命令

- b main：在main处设置断点
  b test.c:16：在test.c的第16行设置断点
- r：从头开始运行程序
- i r：显示寄存器
- c：继续执行
- p 变量名：在程序暂停时查看变量，也可以是函数
- disas 函数名：对一个函数反汇编，查看汇编代码

#### pwndbg

pwndbg是一个gdb插件，可以彩色显示、查看堆栈内容，功能相当强大

安装方法：

```sh
git clone https://github.com/pwndbg/pwndbg
./pwndbg/setup.sh

vim ~/.gdbinit
source /home/mika/Desktop/pwndbg/gdbinit.py
```

直接输入gdb 文件名 即可使用，如果提示缺少module，使用pip3安装即可

调试命令：

- vmmap：查看进程的虚拟内存映射情况
- heap：查看堆块chunk信息
- x /36gx 0x555555756400：查看0x555555756400处的chunk的前36个64位的十六进制数据
- bins