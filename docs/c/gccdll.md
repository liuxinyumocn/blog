# 使用gcc构建C语言编写的动态链接库

​		MacOS下使用gcc构建

创建 foo.c 脚本：

```c
#include <stdio.h>

void foo(void)
{
    printf("foo.\n");
}
```

终端执行：

```shell
#-dynamiclib 表示将foo.c编译成一个动态库
#-o libfoo.dylib 生成的动态库名称
gcc -dynamiclib foo.c -o libfoo.dylib
```

其他脚本使用：

创建 main.c

```c
#include <stdio.h>

extern void foo(void);

int main(void)
{
    foo();
    printf("main.\n");
    return 0;
}
```

终端执行：

```shell
#-L. 指定当前目录为链接时动态库的查找目录，
#-lfoo 指定要链接的动态库为libfoo.dylib，
#-o main 指定生成的可执行文件名称为main
gcc main.c -L. -lfoo -o main

#运行 main
./main
```

