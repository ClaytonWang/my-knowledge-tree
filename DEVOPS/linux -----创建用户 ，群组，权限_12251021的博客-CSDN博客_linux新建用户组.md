# linux -----创建用户 ，群组，权限_12251021的博客-CSDN博客_linux新建用户组
#### 创建用户 ，群组

##### 1、创建用户 :useradd mao1

创建普通用户时，要在 root 下进行创建

##### 2、创建群组 : groupadd

用法和 useradd 相似，后接需要创建的群组名。

##### 3、修改用户账户 : usermod

参数: -l ： 对用户重命名。/home 中的用户家目录名不变，需要手动修改  
-g：修改用户所在的群组  
-G：将一个用户添加到多个群组  
-a：追加 （不想从原来群组中移除，又想添加到新群组）  
记得群组之间，用逗号隔开，而且没有空格。  
\*\* 注意\*\* ：  
使用 usermod 要小心，因为配合 -g 和 -G 参数时。它会把用户从原先的群组了删除，加到新的组里。  
如果不想离开原先的群组，又想添加到新群组里，可以在 - G 的参数基础上加 -a 参数。**在追加群组的时，一定要使用 -G 参数，即使只追加一个群组。**  
我们来看个图来说：  
![](https://img-blog.csdnimg.cn/20191203141037365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3B5X21ldGhvZA==,size_16,color_FFFFFF,t_70)

如上图显示：  
\*\* group\*\*命令单独使用，不添加任何参数，会显示当前用户所在的群组。如下图：  
![](https://img-blog.csdnimg.cn/2019120314220261.png)

##### 4、删除群组 :groupdel

用法：后街想要删除的群组名

##### 5、改变文件的所有者 :chown

\-R 参数：递归设置子目录和 子文件  
\*\* 注意\*\*：这里的 R 只能是大小，小写的 r 不起作用  
假如想要把用户 mao1 的家目录的所有子目录和文件占位所有  
chown -R mao:mao /home/mao1  
则 / home/mao1 都归我（mao）所有了

##### 6、改变文件的群组 :chgrp

用法：后接新的群组名，再接文件名  
其实，chown 命令可以改变文件的群组，用法如下：  
例如 ： \*\* chown mao:friends file.txt\*\*  
就是把 file.txt 文件的所有者改为 mao , 群组改为 friends

##### 7、修改访问权限 :chmod

首先来看下权限的管理 ：  
linux 系统里，每个文件和目录都有一列权限属性。访问权限指明了谁有读的权限，谁有修改的权限，谁有运行的权限。  
访问权限 ：\*\* ls -l\*\*  
文件信息的第一列比较复杂，可以看到不少 d,w,r,l,x 等字母。不细分的话，这些可以通称为 文件访问权限符。  
权限的原理（主要两个）：  
d：表示 “目录”，这是一个目录  
l：表示 “链接”，这是一个链接  
![](https://img-blog.csdnimg.cn/20191203163754744.png)

![](https://img-blog.csdnimg.cn/20191203163931757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3B5X21ldGhvZA==,size_16,color_FFFFFF,t_70)

chmod 命令不需要是 root 用户才能运行  
只要你是此文件的所有者，就可以用 chmod 来修改文件的访问权限  
\*\* 用数字来分配权限：chmod 的绝对权限 \*\*  
![](https://img-blog.csdnimg.cn/20191203164826292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3B5X21ldGhvZA==,size_16,color_FFFFFF,t_70)

\*\* 用字母来分配权限：chmod 的相对权限 \*\*  
u：表示 “所有者”  
g：表示 “群组用户”  
o：表示 “表示其他用户”  
a：表示 “所有用户”

\+ 号：表示 “添加权限”  
-号：表示 “移除权限”  
\\= 号：表示 “分配权限”  
例如：  
chmod u+rx file 文件 file 的所有者增加可读可执行的权限  
chomd g +r file 文件 file 的群组用户增加可读的权限  
chmod o-r file 文件 file 的其他用户移除可读的权限  
chmod g+r o-r file 文件 file 的群组用户增加可读的权限，其他用户移除可读的权限  
chmod go-r file 文件 file 的群组用户，其他用户均移除可读的权限  
chmod +r file 文件 file 的所有的用户都增加可读的权限  
chmod u=rwx,g=r,o=- file 文件 file 的所有者分配可读可写可执行的权限，群组分配可读的权限，其他用户没有权限。

\-R 参数：递归设置子目录和 子文件  
\*\* 注意\*\*：这里的 R 只能是大小，小写的 r 不起作用

#### 总结

有些命令的运行必须要切换到 root 用户权限中去执行  
usermod（用于修改用户账户）—类似 mv 移动用户所在的组  
chgrp（用于修改群组）  
chown（用于修改所有者和群组）

chmod 命令不需要 root 身份就可以执行  
我们可以用 chmod 命令来修改文件的访问权限 
 [https://blog.csdn.net/py_method/article/details/103364504](https://blog.csdn.net/py_method/article/details/103364504)
