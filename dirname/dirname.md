##dirname

功能：截取给定路径的目录部分  
路径：/usr/bin/dirname

参数： 无参数  
用法： dirname dir/file

##用途
dirname命令可以取给定路径的目录部分，一般很少在命令行中使用，更多的是在shell脚本中取得脚本文件所在的目录  

dirname有一处处理方式需要注意：

    [dirname] dirname /usr/bin/ 
    /usr

它并不是去掉最右边一个`/`分割的部分  

basename命令，它与dirname相反，是取得文件名称部分  

##调用流程
base_name函数得到文件名称  
dir_len函数得到最后一个正常`/`的位置


##代码
      1 #include <stdio.h>
      2 #include <stdlib.h>
      3 #include <string.h>
      4 #include <sys/types.h>
      5
      6 #define bool int
      7
      8 #define false 0
      9 #define true  1
     10
     11 # ifndef DIRECTORY_SEPARATOR
     12 #  define DIRECTORY_SEPARATOR '/'
     13 # endif
     14
     15 # ifndef ISSLASH
     16 #  define ISSLASH(C) ((C) == DIRECTORY_SEPARATOR)
     17 # endif
     18
     19 # ifndef FILESYSTEM_PREFIX_LEN
     20 #  define FILESYSTEM_PREFIX_LEN(Filename) 0
     21 # endif
     22
     23 char *base_name (char const *name)
     24 {
     25         char const *base = name + FILESYSTEM_PREFIX_LEN(name);
     26         char const *p;
     27
     28         for(p = base; *p; p++)
     29         {
     30                 if(ISSLASH(*p))
     31                 {
     32                         do p++;
     33                         while(ISSLASH(*p));
     34
     35                         if(! *p)
     36                         {
     37                                 if(ISSLASH(*base))
     38                                 {
     39                                    base = p -1;
     40                                 }
     41                                 break;
     42                         }
     43                         base = p;
     44                 }
     45         }
     46         return (char*)base;
     47 }
     48
     49
     50 size_t dir_len (char const *path)
     51 {
     52         size_t prefix_length = FILESYSTEM_PREFIX_LEN(path);
     53         size_t length;
     54
     55         for (length = base_name(path) - path; prefix_length < length; length--)
     56         {
     57                 if (! ISSLASH(path[length - 1]))
     58                 {
     59                         return length;
     60                 }
     61
     62         }
     63
     64     return prefix_length + ISSLASH(path[prefix_length]);
     65 }
     66
     67 int main(int argc,char **argv)
     68 {
     69         static char const dot ='.';
     70         int use_nuls = false;
     71
     72         char const *result;
     73         size_t  len;
     74
     75         int optind = 1;
     76
     77        for(; optind < argc; optind++)
     78        {
     79           result = argv[optind];
     80
     81           len = dir_len(result);
     82           if( !len)
     83           {
     84                   result = &dot;
     85                   len = 1;
     86           }
     87           fwrite(result,1,len,stdout);
     88           putchar('\n');
     89        }
     90
     91        exit(1);
     92 }


结果：

    [dirname] ./dirname /usr/bin/daige/ee.txt..../// 
    /usr/bin/daige
