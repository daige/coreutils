##hostid
功能：显示当前主机的数字化标识  
路径：/usr/bin/hostid

参数：无  
用法：hostid 

##用途
返回当前主机的数字标识（十六进制，不保证是唯一）


##调用流程
调用gethostid函数返回数字标识(32位值)  
如果gethostid()不能打开/etc/hostid(文件不存在)，  将通过gethostname得到一个hostname,然后传递给gethostbyname_r得到一个ipv4的地址  

##代码
      1 #include <stdio.h>
      2 #include <unistd.h>
      3
      4 int main()
      5 {
      6         unsigned int id;
      7
      8         id = gethostid();
      9
     10         id &=0xffffffff; //保证32位值
     11
     12         printf("0x%08x\n",id);
     13
     14         return 0;
     15 }


##sethostid
      1 #include <stdio.h>
      2 #include <unistd.h>
      3 #include <stdlib.h>
      4
      5 int main()
      6 {
      7         if( sethostid(0x12345678) == 0) //设置hostid
      8         {
      9                 printf("sethostid success\n");
     10                 printf("hostid:0x%08x\n",gethostid());
     11         }
     12         else
     13         {
     14                 printf("sethostid error");
     15                 exit(0);
     16         }
     17
     18         return 0;
     19 }

sethostid执行需要root权限，执行结果：

    [hostid] ./sethostid                                 11:23:22
    sethostid success
    hostid:0x12345678

    [hostid] hostid                                      11:23:24
    12345678
