内核模块代码helloworld.c，编译生成helloworld.o

```c
# include <linux/init.h>
# include <linux/kernel.h>
# include <linux/module.h>

//entry function
static int __init lkm_init(void){
    printk("hello world\n");
    return 0;
}
//exit function
static void __exit lkm_exit(void){
    printk("bye\n");
}
module_init(lkm_init);
module_exit(lkm_exit);
MODULE_LICENSE("GPL");

```

makefile

```makefile
obj-m:=helloworld.o
CURRENT_PATH:=$(shell pwd)
LINUX_KERNEL:=$(shell uname -r)
LINUX_KERNEL_PATH:=/usr/src/linux-headers-$(LINUX_KERNEL)

all:
	make -C $(LINUX_KERNEL_PATH) M=$(CURRENT_PATH) modules

clean:
	make -C $(LINUX_KERNEL_PATH) M=$(CURRENT_PATH) clean

```

添加内核模块

```bash
$ sudo insmod helloworld.ko
```

查看模块和日志（模块的输出）

```bash
$ lsmod
$ dmesg
```

删除模块

```bash
$ sudo rmmod helloworld
```

