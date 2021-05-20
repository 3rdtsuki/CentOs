例如编译命令：

```makefile
g++ -g test.cpp -o test
./test -n 3
```

n是选项

argc是参数个数（但是不需要去填），argv是所有的参数，这里就是[./test -n 3]三个参数



getopt(argc, argv, optstring)：位于头文件<unistd.h>

optstring指定选项合法的选项（如n），一个字符代表一个选项，在字符后面加一个':'表示该选项带一个参数，字符后带两个':'表示该选项带可选参数(参数可有可无)，若有参数，optarg指向该参数，否则optarg为0。如果没有该选项，返回-1

optarg是一个系统指针，指向当前选项参数（如果有）的指针



```cpp
#include <stdio.h>
#include <iostream>
#include <unistd.h>
using namespace std;
int main(int argc, char **argv){//argc是参数个数，argv是所有的参数[./test -n 3]三个参数
	int oc;			 /*选项字符 */
	char *b_opt_arg; /*选项参数字串 */
	while ((oc = getopt(argc, argv, "n:m:")) != -1){//getopt返回int型的asc码
		switch (oc){
			case 'n':
				b_opt_arg = optarg;//optarg：指向当前选项参数(如果有)的指针，这里指向了3
				cout<<b_opt_arg<<endl;
				break;
            case 'm':
				//获取三个参数
				char *x=optarg,*y=argv[optind],*z=argv[optind+1];
				cout<<x<<' '<<y<<' '<<z<<endl;
		}
	}
	// for(int i=0;i<argc;++i){
	// 	printf("%s\n",argv[i]);
	// }
	printf("hello world : %s\n", b_opt_arg);
	return 0;
}
```

如果一个选项想要多个参数

```c
.test -m 3 4 readme.txt
    
char *x=optarg,*y=argv[optind],*z=argv[optind+1];
cout<<x<<' '<<y<<' '<<z<<endl;
```

