# linux 添加用户、权限 
**# useradd –d /usr/sam -m sam**

此命令创建了一个用户 sam，其中 - d 和 - m 选项用来为登录名 sam 产生一个主目录 / usr/sam（/usr 为默认的用户主目录所在的父目录）。

假设当前用户是 sam，则下面的命令修改该用户自己的口令：

**# passwd**

Old password:\*\*\*\*\*\*

New password:\*\*\*\*\*\*\*

Re-enter new password:\*\*\*\*\*\*\*

如果是超级用户，可以用下列形式指定任何用户的口令：

**# passwd sam**

New password:\*\*\*\*\*\*\*

Re-enter new password:\*\*\*\*\*\*\*

**>> 参考 1====================================**

**1、添加用户**

首先用 adduser 命令添加一个普通用户，命令如下：  
#adduser tommy  // 添加一个名为 tommy 的用户  
#passwd tommy   // 修改密码  
Changing password for user tommy.  
New UNIX password:     // 在这里输入新密码  
Retype new UNIX password:  // 再次输入新密码  
passwd: all authentication tokens updated successfully.  
**2、赋予 root 权限**方法一：修改 /etc/sudoers 文件，找到下面一行，把前面的注释（#）去掉  
## Allows people in group wheel to run all commands  
%wheel    ALL=(ALL)    ALL  
然后修改用户，使其属于 root 组（wheel），命令如下：  
#usermod -g root tommy  
修改完毕，现在可以用 tommy 帐号登录，然后用命令 su - ，即可获得 root 权限进行操作。  
方法二：修改 /etc/sudoers 文件，找到下面一行，在 root 下面添加一行，如下所示：  
## Allow root to run any commands anywhere  
root    ALL=(ALL)     ALL  
tommy   ALL=(ALL)     ALL  
修改完毕，现在可以用 tommy 帐号登录，然后用命令 su - ，即可获得 root 权限进行操作。

**>> 参考 2====================================**

**用 useradd 新增的用户不能直接用 ssh 远程访问，需要修改 ssh 相关配置**如下：  
vi /etc/ssh/sshd_config   
添加

AllowUsers root@192.168.1.32 admin  
多个用户用空格隔开

**>> 参考 3====================================**  
**如何让普通用户获得 root 用户的权限执行操作**而不需要知道 root 用户的密码或向 root 用户进行切换呢？有一个命令 sudo 可以实现这个功能.  
Sudo 的工作流程：  
（1）root 用户编辑 / etc/sudoers 文件, 添加要分配的普通用户记录, 其中有这么一行记录：root ALL=(ALL) ALL, 在这行后面添加：Sam ALL=(ALL) ALL  
(2) 那么 sam 用户在执行操作时, 如果所执行的操作自己没有权限, 则会去 / etc/sudoers 文件中查找是否有对应的记录, 如果有的话则临时获得 root 权限, 执行只有 root 才能执行的操作.  
注意：这里会提示输入密码, 但是输入的是 sam 用户的密码, 而不是 root 用户的密码. 例如：  
Shell> sudo mkdir –p a/b/c/d  
We trust you have received the usual lecture from the local System  
Administrator. It usually boils down to these three things:  
#1) Respect the privacy of others.  
#2) Think before you type.  
#3) With great power comes great responsibility.  
Password: #这里的密码是 sam 用户自己的密码, 而不是 root 的密码  
几个操作：  
1) 编辑 / etc/sudoers 文件, 使用 visudo 命令,  
Shell> visudo  
其实 visudo 命令也是调用 vi 去编辑 sudoer 文件的, 但是在保存时会去检查你修改后文件的语法, 如果错误是不能保存的, 但是如果直接用 vi 来编辑该文件, 强制保存虽然成功, 但是如果有语  
法错误这里并不能给出提示, 也就导致了后面 sudo 命令的不可用.  
2) 记录解析：  
Sam ALL=(ALL) ALL  
使用者帐户 登入的主机 =（可以变换的身份） 可以下达的命令  
上述语句的意思是 sam 用户可以在任何地方登录, 并可切换成任何用户进行任何操作. 这里如果（ALL）不写, 只写为 sam ALL=ALL, 他默认只能切换为 root 用户.

**>> 参考 4====================================**

**linux chown 命令参数及用法详解 -- 改变档案的所有者:**

要更改文件 program.c 的所有者：**chown jim program.c**program.c 的用户访问权限现在应用到 jim。作为所有者，jim 可以使用 [chmod](http://www.linuxso.com/command/chmod.html) 命令允许或拒绝其他用户访问 program.c。

要将目录 /tmp/src 中所有文件的所有者和组更改为用户 john 和组 build：**chown -R john:build /tmp/src**

如: **tomcat 的安装目录的所有者修改 (#chown -R user:group /usr/java/tomcat5.5)**

**>> 参考 5====================================**

linux 下添加，删除，修改，查看用户和用户组

1，创建组

groupadd test

增加一个 test 组

2，修改组

groupmod -n test2 test

将 test 组的名子改成 test2

3，删除组

groupdel test2

删除 组 test2

4，查看组

a），查看当前登录用户所在的组 groups，查看 apacheuser 所在组 groups apacheuser

b），查看所有组 cat /etc/group

c），有的 linux 系统没有 / etc/group 文件的，这个时候看下面的这个方法

cat /etc/passwd |awk -F \[:] ‘{print $4}’ |sort|uniq | getent group |awk -F \[:] ‘{print $1}’

这里用到一个命令是 getent, 可以通过组 ID 来查找组信息, 如果这个命令没有的话, 那就很难查找, 系统中所有的组了.

二，用户操作

1，增加用户

查看复制打印?

\[root@krlcgcms01 mytest]# useradd –help

Usage: useradd \[options] LOGIN

Options:

\-b, –base-dir BASE_DIR       设置基本路径作为用户的登录目录

\-c, –comment COMMENT         对用户的注释

\-d, –home-dir HOME_DIR       设置用户的登录目录

\-D, –defaults                改变设置

\-e, –expiredate EXPIRE_DATE 设置用户的有效期

\-f, –inactive INACTIVE       用户过期后，让密码无效

\-g, –gid GROUP               使用户只属于某个组

\-G, –groups GROUPS           使用户加入某个组

\-h, –help                    帮助

\-k, –skel SKEL_DIR           指定其他的 skel 目录

\-K, –key KEY=VALUE           覆盖 /etc/login.defs 配置文件

\-m, –create-home             自动创建登录目录

\-l,                           不把用户加入到 lastlog 文件中

\-M,                           不自动创建登录目录

\-r,                           建立系统账号

\-o, –non-unique              允许用户拥有相同的 UID

\-p, –password PASSWORD       为新用户使用加密密码

\-s, –shell SHELL             登录时候的 shell

\-u, –uid UID                 为新用户指定一个 UID

\-Z, –selinux-user SEUSER     use a specific SEUSER for the SELinux user mapping

\[root@krlcgcms01 mytest]# useradd --help

Usage: useradd \[options] LOGIN

Options:

 \-b, --base-dir BASE_DIR       设置基本路径作为用户的登录目录

 \-c, --comment COMMENT         对用户的注释

 \-d, --home-dir HOME_DIR       设置用户的登录目录

 \-D, --defaults                改变设置

 \-e, --expiredate EXPIRE_DATE 设置用户的有效期

 \-f, --inactive INACTIVE       用户过期后，让密码无效

 \-g, --gid GROUP               使用户只属于某个组

 \-G, --groups GROUPS           使用户加入某个组

 \-h, --help                    帮助

 \-k, --skel SKEL_DIR           指定其他的 skel 目录

 \-K, --key KEY=VALUE           覆盖 /etc/login.defs 配置文件

 \-m, --create-home             自动创建登录目录

 \-l,                           不把用户加入到 lastlog 文件中

 \-M,                           不自动创建登录目录

 \-r,                           建立系统账号

 \-o, --non-unique              允许用户拥有相同的 UID

 \-p, --password PASSWORD       为新用户使用加密密码

 \-s, --shell SHELL             登录时候的 shell

 \-u, --uid UID                 为新用户指定一个 UID

 \-Z, --selinux-user SEUSER     use a specific SEUSER for the SELinux user mappinguseradd test

passwd test

增加用户 test，有一点要注意的，useradd 增加一个用户后，不要忘了给他设置密码，不然不能登录的。

2，修改用户

usermod -d /home/test -G test2 test

将 test 用户的登录目录改成 / home/test，并加入 test2 组，注意这里是大 G。

gpasswd -a test test2 将用户 test 加入到 test2 组

gpasswd -d test test2 将用户 test 从 test2 组中移出

3，删除用户

userdel test

将 test 用户删除

4，查看用户

a），查看当前登录用户

\[root@krlcgcms01 ~]# w

\[root@krlcgcms01 ~]# who

b），查看自己的用户名

\[root@krlcgcms01 ~]# whoami

c），查看单个用户信息

\[root@krlcgcms01 ~]# finger apacheuser

\[root@krlcgcms01 ~]# id apacheuser

d），查看用户登录记录

\[root@krlcgcms01 ~]# last 查看登录成功的用户记录

\[root@krlcgcms01 ~]# lastb 查看登录不成功的用户记录

e），查看所有用户

\[root@krlcgcms01 ~]# cut -d : -f 1 /etc/passwd

\[root@krlcgcms01 ~]# cat /etc/passwd |awk -F \\: ‘{print $1}’ 
 [https://www.cnblogs.com/daizhuacai/archive/2013/01/17/2865132.html](https://www.cnblogs.com/daizhuacai/archive/2013/01/17/2865132.html)
