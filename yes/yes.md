##yes

功能：重复输出字符串  
路径：/usr/bin/yes  

参数： yes +字符串  
用法： yes daige  

##用途
- yes 不加参数，循环输出`y`,可用于自动回答命令行提示  
例如：
    <pre><code>
    [test] ls -l                                                     10:44:06 
    total 8
    -rw-r--r-- 1 root root 6 Sep 24 10:43 test.c
    -rw-r--r-- 1 root root 5 Sep 24 10:44 test.cpp
    </code></pre>
    使用yes自动回答`y`:  
    <pre><code>
    [test] yes | rm -i test*                                         10:44:56 
    rm: remove regular file `test.c'? rm: remove regular file `test.cpp'?  
   </code></pre>
- 生成大文本文件
    <pre><code>
    1 #!/bin/bash
    2 
    3 yes daige > daige.txt &
    4 PID=$!
    5 sleep 1
    6 kill $PID
    7 
    8 ls -l daige.txt
    </code></pre>
    脚本把yes输出的内容保存在文件中，一秒钟之后，杀掉，生成了80M的大文本。  
    也可以使用命令行：    
    `[yes] yes > hello.txt`  
    等3秒钟，杀死进程，查看大小：   
    <pre><code>
    [yes] ll -h hello.txt                                   10:56:58 
    -rw-r--r-- 1 root root 250M Sep 24 10:56 hello.txt
    </code></pre>

##调用流程
无

##代码
      1 #include <stdio.h>
      2 #include <stdlib.h>
      3 
      4 int main(int argc,char**argv)
      5 {
      6         if(argc <=  1)
      7         {
      8                 argv[argc++] = "y";
      9         }
     10 
     11         while(1)
     12         {
     13                 int i;
     14                 for(i=1; i<argc; i++)
     15                 {
     16                   if( fputs(argv[i],stdout) == EOF || putchar (i == argc-1 ?'\n':' ') == EOF)
     17                   {
     18                           printf("error");
     19                           exit(0);
     20                   }
     21                 }
     22         }
     23 
     24         return 0;
     25 }

