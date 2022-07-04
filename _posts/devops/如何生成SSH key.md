# 如何生成SSH key
# 如何生成 SSH key

SSH key 提供了一种与 GitHub 通信的方式，通过这种方式，能够在不输入密码的情况下，将 GitHub 作为自己的 remote 端服务器，进行版本控制

## 步骤

-   检查 SSH keys 是否存在
-   生成新的 ssh key
-   将 ssh key 添加到 GitHub 中

    ![](https://upload-images.jianshu.io/upload_images/2160098-c541ffe319a1b3ab.png)

    gevin-essay-how-to-generate-SSH-key.png

## 1. 检查 SSH keys 是否存在

输入下面的命令，如果有文件`id_rsa.pub` 或 `id_dsa.pub`，则直接进入步骤 3 将 SSH key 添加到 GitHub 中，否则进入第二步生成 SSH key

## 2. 生成新的 ssh key

**第一步：生成 public/private rsa key pair**  
在命令行中输入`ssh-keygen -t rsa -C "your_email@example.com"`

默认会在相应路径下（/your_home_path）生成`id_rsa`和`id_rsa.pub`两个文件，如下面代码所示

    gcl:~ smartlei$ ssh-keygen -t rsa -C "http://igit.58corp.com"
    Generating public/private rsa key pair.
    //说明会在这个路径下/Users/smartlei/.ssh/id_rsa 生成id_rsa和id_rsa.pub文件，
    //如果你敲入别的地址则为在别的地址生生成，例如我在/Users/smartlei/.ssh/id_rsa_igit
    //这个文件下生成id_rsa_igit 文件文件名也可以修改（）
    Enter file in which to save the key (/Users/smartlei/.ssh/id_rsa): /Users/smartlei/.ssh/id_rsa_igit
    Enter passphrase (empty for no passphrase): 
    Enter same passphrase again: 
    Your identification has been saved in /Users/smartlei/.ssh/id_rsa_igit.
    Your public key has been saved in /Users/smartlei/.ssh/id_rsa_igit.pub.
    The key fingerprint is:
    SHA256:++Ik2wTYxuEz5UP/G+XEo7ju8pM8SZhol0ifwdZa15U http://igit.58corp.com
    The key's randomart image is:
    +---[RSA 2048]----+
    |                .|
    |               E.|
    |      ..o.   . . |
    |     =.=+.o ...  |
    |    ..O=SO..  =  |
    |     .++Oo.o = . |
    |     ...+o.o+ .  |
    |       *o.*. o   |
    |      ..o**o.    |
    +----[SHA256]-----+ 

#### "[your_email@example.com](https://links.jianshu.com/go?to=mailto%3Ayour_email%40example.com)" 邮箱干嘛用的

> 每次生成密钥，看到国内的博客，都要求输入邮箱。但不知道意义何在。  
> 后来在 stackOverFlow 上看到这个答案。  
> The email is only optional field to identify the key.  
> 邮箱仅仅是识别用的 key  
> When you create the ssh you type (for example):  
> ssh-keygen -t rsa -C “any comment can be here”  
> 当你创建 ssh 的时候  
> -t = The type of the key to generate  
> 密钥的类型  
> -C = comment to identify the key  
> 用于识别这个密钥的注释  
> So the Comment is for you only and you can put anything inside.  
> Many sites and software are using this comment as the key name.  
> 所以这个注释你可以输入任何内容，很多网站和软件用这个注释作为密钥的名字

**第二步：输入 passphrase(密码: 某些网站 clone 时需要密码)（本步骤可以跳过）**

设置 passphrase 后，进行版本控制时，每次与 GitHub 通信都会要求输入 passphrase，以避免某些 “失误”

sample result:

**第三步：将新生成的 key 添加到 ssh-agent 中:**

    ssh-add ~/.ssh/id_rsa 

> Mac 系统内置了一个 Keychain 的服务及其管理程序，可以方便的帮你管理各种秘钥，其中包括 ssh 秘钥。  
> ssh-add 默认将制定的秘钥添加在当前运行的 ssh-agent 服务中，但是你可以改变这个默认行为让它添加到 keychain 服务中，让 Mac 来帮你记住、管理并保障这些秘钥的安全性。  
> 你所要做的就是执行下面的命令：

    $ ssh-add -K [path/to/your/ssh-key] 

之后，其他的程序请求 ssh 秘钥的时候，会通过 Keychain 服务来请求。

## 3. 将 ssh key 添加到 GitHub 中

用自己喜欢的文本编辑器打开`id_rsa.pub`文件，里面的信息即为 SSH key，将这些信息复制到 GitHub 的`Add SSH key`页面即可

不同的操作系统，均有一些命令，直接将 SSH key 从文件拷贝到粘贴板中，如下：

**mac**

**windows**

**linux** 
 [https://www.jianshu.com/p/259773014e03](https://www.jianshu.com/p/259773014e03)
