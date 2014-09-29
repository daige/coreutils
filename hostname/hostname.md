##hostname
功能：显示或者设置主机名  
路径：/usr/bin/hostname

参数：无  
用法：hostname [新主机名]

##用途
显示或者设置主机名   
- hostname 显示主机名    
- hostname  daige 设置daige为新的主机名 
    
注意：现在常用的hostname版本并不是coreutils版本，使用时请`hostname -h `    
在修改主机名时，请先关闭依赖系统主机名的软件，永久修改需要同时修改
`/etc/hosts`和`/etc/sysconfig/network`的相关内容

##调用流程
设置主机名调用sethostname函数(for SVR4 mechanism)  
显示主机名调用xgethostname函数  
在代码中，只使用了gethostname函数，需要了解xgethostname函数实现请点击[这里](./code/xgethostname.c)

##代码

      1 #include <stdio.h>
      2 #include <stdlib.h>
      3 #include <unistd.h>
      4
      5 #if !defined HAVE_SETHOSTNAME && defined HAVE_SYSINFO && \
      6                defined HAVE_SYS_SYSTEMINFO_H
      7 #include <sys/systeminfo.h>
      8
      9 static int sethostname(char *name,size_t name_len)
     10 {
     11         return (sysinfo(SI_SET_HOSTNAME,name,name_len) < 0?-1:0);
     12 }
     13
     14
     15 #define HAVE_SETHOSTNAME  1
     16 #endif
     17
     18 char * xgethostname()
     19 {
     20 //替换gethostname函数
     21 }
     22
     23 int main(int argc,char **argv)
     24 {
     25         char *hostname=malloc(100*sizeof(char));
     26
     27         if(argc == 2)
     28         {
     29           #ifdef HAVE_SETHOSTNAME
     30
     31           char const *name = argv[1];
     32
     33           if(sethostname(name,strlen(name))!= 0)
     34           {
     35                   printf("sethostname error");
     36                   exit(0);
     37           }
     38
     39           #else
     40           printf("this system lacks the function");
     41
     42           #endif
     43         }
     44
     45         if( argc == 1)
     46         {
     47           if( gethostname(hostname,100) != 0 )
     48           {
     49                   printf("gethostname error");
     50           }
     51
     52           printf("%s\n",hostname);
     53
     54         }
     55
     56         free(hostname);
     57         return 0;
     58 }

##hosts文件格式
`/etc/hosts`文件的格式如下：  

     1 127.0.0.1       localhost
     2 127.0.1.1       kali.192.168.1.100      kali

`gethostname`函数会去读hosts文件:  

    [hostname] grep kali /etc/hosts                   
    127.0.1.1       kali.192.168.1.100      kali
