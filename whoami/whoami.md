##whoami

功能：显示登录名  
路径：/usr/bin/whoami

参数： 无参数  
用法： whoami

##用途
- whoami　　　当前操作用户名 　有效用户ID 即euid
- who am i 　等同于who -m 　　实际用户的用户名 即uid
- who　　　　当前用户登录

##调用流程
geteuid函数得到uid  
getpwuid函数根据uid得到passwd结构  

##代码

      1 #include <stdio.h>
      2 #include <sys/types.h>
      3 #include <pwd.h>
      4
      5 int main()
      6 {
      7         struct passwd *pw;
      8         uid_t uid;
      9
     10         uid = geteuid();
     11         pw = getpwuid(uid);
     12
     13         puts(pw->pw_name);
     14
     15         return 0;
     16 }
    ~

结果：

    [whoami] ./whoami       
    root

##passwd结构
`/etc/passwd`文件的结构：

     49 struct passwd
     50 {
     51   char *pw_name;                /* Username.  */
     52   char *pw_passwd;              /* Password.  */
     53   __uid_t pw_uid;               /* User ID.  */
     54   __gid_t pw_gid;               /* Group ID.  */
     55   char *pw_gecos;               /* Real name.  */
     56   char *pw_dir;                 /* Home directory.  */
     57   char *pw_shell;               /* Shell program.  */
     58 };
