## 第一章
### 头文件

使用```-I```标志来包含保存在子目录或在非标准位置中的头文件。
例如``` gcc -I/usr/openwein/include fred.c ```表示在```/usr/openwein/include ```目录中查找程序``` fred.c ```

---
### 库文件

标准库文件一般在```/lib```或者```/usr/lib```目录中。
- ```.a```代表静态库
- ```.so```代表共享函数库

通过完整的库文件路径名或用```-l```标志来告诉编译器要搜索的库文件。
例如：```gcc -o fred fred.c /usr/lib/libm.a``` or ```gcc -o fred fred.c -lm```
虽然库文件和头文件一样，通常都保存在标准位置，但你也可以通过使用```-L```标志为编译器增加搜索库文件的路径。
例如：```gcc -o x11fred -L/usr/openwin/lib x11fred.c -lX11 ``` 
#### 静态库
首先创建两个源文件（fred.c and bill.c）

```
//fred.c

#include <stdio.h>
void fred(int arg){
  printf("fred: we passed %d\n",arg);
}

//bill.c

#include <stdio.h>
void bill(char* arg){
  printf("bill: we passed %s\n",arg);
}
```
使用```-c```编译成目标文件，```-c```可以阻止编译器创建一个完整的程序
```gcc -c bill.c fred.c ``` 生成 ```bill.o fred.o  ```两个文件
再编写一个头文件```lib.h```
```
//lib.h
void bill(char *);
void fred(int);
```
编写一个主函数```program.c```
```
//program.c
#include <stdio.h>
#include "lib.h"

int main(){
    bill("hello world\n");
    exit(0);
}
```
使用3种方法编译
- 显式指定目标文件
```
$ gcc -c program.c
$ gcc -o program program.o bill.o
$ ./program 
bill: we passed hello world
$
```
- 使用静态库 ```ar``` 程序将目标文件归档成静态库```.a``` 部分Berkeley Unix衍生系统要使用函数库还需要为函数库生成一个内容表，可用```ranlib```实现
```
$ ar crv libfoo.a bill.o fred.o
a - bill.o
a - fred.o
$ ranlib libfoo.a
$ gcc -o program program.o libfoo.a 
$ ./program 
bill: we passed hello world
$
```
- 使用```-l```选项 ```-L.```告诉编译器在前目录查找函数库，```-lfoo```告诉编译器查找```libfoo.a```(或者```libfoo.so```的共享库，如果存在的话)
```
$ gcc -o program program.o -L. -lfoo
$ ./program 
bill: we passed hello world
$
```
