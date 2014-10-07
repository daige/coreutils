##true

功能：什么都不做，设置退出码为0  
路径：/bin/true

参数： 无参数  
用法： true

##用途
设置退出码为0   
相同的内建命令 `:` 也能达到效果  

    [true] true;echo $?                                       
    0

测试false和`:`: 

    [true] false;echo $?;true;echo $?;false;:;echo $?  
    1
    0
    0

false是相反的命令，设置退出码为1  

主要用于死循环 while true;dosomthing; done   
当然也可while:; dosomthing; done

##调用流程
无

##代码
      1 #include <stdio.h>
      2 #include <stdlib.h>
      3
      4 int main()
      5 {
      6         exit(0);
      7 }
    ~

结果：

    [true] false;./true;echo $?   
    0

