##logname

功能：显示登录名称  
路径：/usr/bin/logname

参数：无参数  
用法：logname  

##用途
用于显示当前进程的登录名称，对应于系统状态环境中的LOGNAME变量。只有当用户登录进系统的时候才会设置此变量。 

##调用流程
调用getlogin函数返回当前登录名


##代码
      1 #include <stdio.h>
      2 #include <unistd.h>
      3 #include <stdlib.h>
      4 
      5 int main()
      6 {
      7         char *logname=NULL;
      8 
      9         logname = getlogin();
     10         if(logname)
     11         {
     12                 printf("%s\n",logname);
     13                 return 0;
     14         }
     15         else
     16         {
     17                 printf("getlogin() error!");
     18                 exit(1);
     19         }
     20       
     21 }


##关于utmp和wtmp 
utmp文件，记录当前登录的各个用户 　路径/var/run/utmp  
wtmp文件，记录各个登录和注销事件 　路径/var/log/wtmp    

读取这两个文件，需要专有的结构utmp和wtmp   
utmp例子：

      1 #include <stdio.h>
      2 #include <unistd.h>
      3 #include <stdlib.h>
      4 #include <utmp.h>
      5 #include <fcntl.h>
      6 #include <time.h>
      7 
      8 //#define UTMP_FILE ("/var/run/utmp")
      9 
     10 int main()
     11 {
     12        int fd;
     13        struct utmp current_record;
     14        int  record_len =sizeof(struct utmp);
     15 
     16        if((fd = open(UTMP_FILE,O_RDONLY)) != -1)
     17        {
     18          while((read(fd,&current_record,record_len)) == record_len)
     19          {
     20              printf("%s   %s  ",current_record.ut_line,
                                        current_record.ut_name);
     21              printf("%s  ",current_record.ut_host);
     22              printf("%s",ctime(&current_record.ut_time));
     23          }
     24        }
     25 
     26       close(fd);
     27       return 0;
     28 }

结果：
  <pre><code>
  [logname] ./utmp                                               23:11:51 
  pts/1   root  :0.0  Sun Sep 21 23:09:53 2014
  pts/0   root  :0.0  Sun Sep 21 21:55:50 2014
  pts/2   root    Sun Sep 21 21:55:53 2014
</code></pre>


