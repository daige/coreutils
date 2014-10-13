##tty

功能：打印标准输入的文件名称  
路径：/usr/bin/tty

参数：
   - s   不打印信息，根据退出状态来判断标准输入是不是终端  
         是标准输入，退出状态返回0，不是返回1

用法： tty

##用途
在Linux系统中，终端是一种字符型设备，它有多种类型，通常使用tty来简称各种类型的终端设备。tty是Teletype的缩写，Teletype是最早出现的一种终端设备，很像电传打字机，是由Teletype公司生产的  

在Linux系统中，终端设备非常重要，没有终端设备，系统将无法向用户反馈信息，Linux中包含控制台、串口和伪终端3类终端设备  

tty不带参数，会打印准备输入的文件名称    
如果标准输入不是终端设备时，打印not a tty   
否则打印终端设备名称，比如 /etc/tty1、/etc/pts/1

    [tty] tty  
    /dev/pts/5


##调用流程
调用ttyname得到标准输入的文件名称  
调用isatty获取设备的类型来确定退出状态

##代码

      1 #include <sys/types.h>
      2 #include <string.h>
      3 #include <unistd.h>
      4 #include <stdlib.h>
      5
      6 #define  true  1
      7 #define  false 0
      8
      9 static  int    silent;
     10
     11 int main(int argc,char **argv)
     12 {
     13         char *tty;
     14
     15         silent = false;
     16         if( argc == 2)
     17         {
     18                 if(strcmp(argv[1],"-s") == 0)
     19                 {
     20                         silent = true;
     21                 }
     22         }
     23
     24         tty = ttyname(STDIN_FILENO);
     25
     26         if( !silent )
     27         {
     28                 if( tty )
     29                 {
     30                         puts(tty);
     31                 }
     32                 else
     33                 {
     34                         puts("not a tty");
     35                 }
     36         }
     37
     38     exit(isatty(STDIN_FILENO) ? 0:1);
     39 }
            

结果：  

    [tty] ./tty 
    /dev/pts/2
    [tty] tty   
    /dev/pts/2
    [tty] ./tty -s   
    [tty] tty -s   


####关于STDIN_FILENO
STDIN_FILENO是系统API的接口，是一个int型的变量  
系统级的调用如open/read/write/close等函数都会使用它  
STDIN_FILENO就是标准输入设备（一般是键盘）的文件描述符  
定义是在`/usr/include/unistd.h`

    /* Standard file descriptors. */
    #define STDIN_FILENO 0 /* Standard input. */
    #define STDOUT_FILENO 1 /* Standard output. */
    #define STDERR_FILENO 2 /* Standard error output. */


####STDIN_FILENO和stdin的区别
- stdin类型为FILE *,使用它的函数主要是fread,fwrite,fclose等文件操作函数  
  STDIN_FILENO类型为int，使用它的函数主要是open,read,write,close等系统函数  

- stdin属于标准I/O，高级的输入输出函数，在`<stdio.h>`定义  
  STDIN_FILENO属于系统API，属于没有buffer的I/O，直接调用系统调用，在`<unistd.h>`定义





