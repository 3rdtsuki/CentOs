#### c程序加参数

例如编译命令：

```makefile
gcc -g test.c -o test
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
				break;
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

#### 计算运行时间

```c
#include <sys/time.h>
#include <unistd.h>
 
#define TIC                                                             \
    struct timeval time1, time2;                                        \
    gettimeofday(&time1, nullptr);
 
#define TOC                                                             \
    gettimeofday(&time2, nullptr);                                      \
    double elapsed_time = (time2.tv_sec - time1.tv_sec) * 1000. +       \
                (time2.tv_usec - time1.tv_usec) / 1000.;                \
    fprintf(stdout, "Elapsed time: %lf(ms)\n", elapsed_time);
 
int main()
{
    TIC
    sleep(2);
    TOC
 
    return 0;
}
```

#### 访问mysql

编译

```sh
gcc -g sql.c -o sql -lmysqlclient -I/usr/include/mysql/ -L/usr/lib/mysql
```

```c
#include <mysql/mysql.h>
#include <stdio.h>

MYSQL conn;
void connection(const char* host, const char* user, const char* password, const char* database) {  
    mysql_init(&conn);

    if (mysql_real_connect(&conn, host, user, password, database, 0, NULL, 0)) {  
        printf("Connection to MYSQL success!\n");  
    } else {  
        fprintf(stderr, "Connection to MYSQL failed!\n");  
        if (mysql_errno(&conn)) {  
            fprintf(stderr, "Connection error %d: %s\n", mysql_errno(&conn), mysql_error(&conn));  
        }  
        exit(EXIT_FAILURE);  
    }  
}
int main(){
    char username[128];
    printf("Please input your username:\n");
    scanf("%s",username);
    printf("%s\n",username);
    connection("localhost", "mika", "1798", "mika_lsm"); 

    MYSQL_RES *result;
    MYSQL_ROW row;
    char buf[100];
    sprintf(buf,"select * from user_role where user='%s';",username);
    mysql_query(&conn, buf);
    result = mysql_store_result(&conn); 
    int r = mysql_num_rows(result);
    int c = mysql_num_fields(result);
    for(int i=0;i<r;++i){
        row = mysql_fetch_row(result);
        for(int j=0;j<c;++j){
            printf("%s ",row[j]);
        }
        printf("\n");
    }
    mysql_free_result(result);
}
```

