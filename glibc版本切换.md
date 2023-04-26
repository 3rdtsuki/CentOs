#### ubuntu切换glibc版本

glibc是GNU发布的C语言标准库，不同版本的glibc中函数实现不同，可能导致程序运行结果不同。这里介绍如何切换不同版本的glibc来运行程序

首先，要安装patchelf，这是给elf文件打补丁（修改elf文件属性，包括使用的glibc版本）的工具

```sh
git clone https://github.com/NixOS/patchelf.git
cd patchelf
./bootstrap.sh
./configure
make
make check
sudo make install
```

注意，如果make时报很多错，是因为gcc和g++版本过低，使用如下命令可以从gcc/g++7升级到11

```sh
sudo apt-get install gcc-11
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/g++-7 20
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/g++-11 50
sudo apt-get install g++-11
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-7 20
sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 50
```

接下来安装glibc-all-in-one来下载我们需要的glibc，这里以下载glibc2.23为例。注：`ldd --version`可以查看当前glibc版本

```sh
git clone https://github.com/matrix1001/glibc-all-in-one
cd glibc-all-in-one/
python3 update_list
cat list
./download 2.23-0ubuntu11.3_amd64
```

最后就是给我们的程序打补丁了，将patchelf命令中两个路径换成本地glibc对应的路径，最后是要打补丁的程序名，运行即可

```sh
gcc -g test.c -o test
patchelf --set-interpreter /home/mika/Downloads/glibc-all-in-one/libs/2.23-0ubuntu11.3_amd64/ld-2.23.so --set-rpath /home/mika/Downloads/glibc-all-in-one/libs/2.23-0ubuntu11.3_amd64 test
./test
```

