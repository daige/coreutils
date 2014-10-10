##unlink

功能：删除一个目录项  
路径：/usr/bin/unlink

参数： 无参数  
用法： unlink file

##用途
从文件系统中删除一个目录项，如果目录项是这个文件的最后一个链接而且并没有进程打开它，那么这个文件就会被删除  

##调用流程
unlink函数解除对一个目录项的链接

##代码

      1 #include <stdio.h>
      2 #include <stdlib.h>
      3 #include <sys/types.h>
      4
      5 int main(int argc,char **argv)
      6 {
      7         if( argc == 2)
      8         {
      9                 if(unlink(argv[1]) != 0)
     10                 {
     11                    printf("cannot unlink %s ",argv[1]);
     12                 }
     13         }
     14         else
     15         {
     16                 printf("unlink file\n");
     17                 exit(0);
     18         }
     19
     20         return 0;
     21 }
     22
     23
    ~


结果：

    [unlink] ls -ail test*   
    695322 -rw-r--r-- 2 root root 261 Oct 10 14:19 test
    695322 -rw-r--r-- 2 root root 261 Oct 10 14:19 test.c
    [unlink] ./unlink test   
    [unlink] ls -ail test*  
    695322 -rw-r--r-- 1 root root 261 Oct 10 14:19 test.c

建立一个对test.c的硬链接test，调用unlink解除test，结果删除了test,链接数也从2变成了1

##文件信息
Linux下的每个文件，都可以通过一个struct stat的结构得到文件的信息，  
struct stat的结构如下：  

           struct stat {
               dev_t     st_dev;     /* ID of device containing file */
               ino_t     st_ino;     /* inode number */
               mode_t    st_mode;    /* protection */
               nlink_t   st_nlink;   /* number of hard links */
               uid_t     st_uid;     /* user ID of owner */
               gid_t     st_gid;     /* group ID of owner */
               dev_t     st_rdev;    /* device ID (if special file) */
               off_t     st_size;    /* total size, in bytes */
               blksize_t st_blksize; /* blocksize for file system I/O */
               blkcnt_t  st_blocks;  /* number of 512B blocks allocated */
               time_t    st_atime;   /* time of last access */
               time_t    st_mtime;   /* time of last modification */
               time_t    st_ctime;   /* time of last status change */
           };

stat结构中的st_nlink代表文件的硬链接数，如果链接数为0，此文件的内容才会被真正的删除  

能够影响链接数的操作有：link,unlink和open的创建文件  

删除文件的本质操作是unlink

下面用一个程序来说明：

      1 #include <stdio.h>
      2 #include <stdlib.h>
      3 #include <sys/types.h>
      4 #include <sys/stat.h>
      5 #include <fcntl.h>
      6 #include <unistd.h>
      7
      8 int main()
      9 {
     10         int fd;
     11         char buff[16];
     12         struct stat file_stat;
     13         //open创建文件 链接数为1
     14         if((fd = open("daige",O_RDWR|O_CREAT|O_TRUNC)) < 0)
     15         {
     16                  printf("cannot create file \n");
     17         }
     18
     19         stat("daige",&file_stat);
     20         printf("daige  link=%d\n",file_stat.st_nlink); //输出1
     21
     22         link("daige","link_daige"); //link_daige链接 链接数+1
     23
     24         stat("daige",&file_stat);
     25         printf("daige after link=%d\n",file_stat.st_nlink); //输出2
     26
     27         if(unlink("link_daige") < 0)  //unlink link_daig 链接数-1
     28         {
     29            printf("unlink error\n");
     30         }
     31
     32         stat("daige",&file_stat);
     33         printf("after unlink link_daige link=%d\n",file_stat.st_nlink);
     34         //输出1
     35         if(write(fd,"temp",5) < 0)
     36         {
     37                 printf("write error");
     38         }
     39
     40         if((lseek(fd,0,SEEK_SET)) == -1)
     41         {
     42                 printf("lseek error");
     43         }
     44
     45         if((read(fd,buff,5)) < 0)
     46         {
     47                 printf("read error");
     48         }
     49
     50         printf("%s\n",buff);
     51
     52         close(fd);
     53         return 0;
     54 }


结果：

    [unlink] ./stat               
    daige  link=1
    daige after link=2
    after unlink link_daige link=1
    temp
