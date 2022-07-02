# nvm：Mac下的 node 版本管理工具 
* * *

## permalink:/DEVOPS/mac_install_nvm.html

> 日常开发中，可能我们好几个项目依赖的 NodeJS 版本是不同的，如果没有一个合适的管理工具，有时候真的很抓狂，这个时候，就是 nvm 发挥作用的时候了

## 安装 nvm

可以使用命令

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash

```

或者使用 `Wget`:

```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash

```

安装完成后最好关闭终端，然后重启输入 `nvm`验证是否安装成功，如果出现`Node Version Manager`，说明安装是成功的。

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5164b7d8f0~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

但如果提示是

```
command not found: nvm

```

可能是缺少相应配置导致的，可以检查根目录下的这些文件`(~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc).`，可在底部添加如下代码：

```
export NVM_DIR="${XDG_CONFIG_HOME/:-$HOME/.}nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm

```

保存并更新配置文件

可以输入`command -v nvm`查看结果

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5168b9e1f5~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

## nvm 常用命令

### 查看可以安装的 node 版本 (官方所有能用版本)

```
nvm ls-remote

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5164c75f99~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 查看所有可以安装的 LTS 版本（长期支持版）

```
nvm ls-remote --lts

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d516881b498~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 安装指定版本的 node

```
nvm install v9.5.0

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d51692553c2~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

v 后面是想要安装的版本号

### 查看已安装的 node

```
nvm ls

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5169390ec2~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 切换 node 版本

```
nvm use v6.9.0

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5192f5bcd8~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 设定默认的 node 版本

```
nvm alias default v6.9.0

```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d51c83361c1~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

打开新的终端，用`nvm current`查看当前版本显示

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d5188f5157a~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 删除指定版本的 node

```
nvm uninstall v9.5.0

```

此过程会花费一点时间

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/5/7/16a90d518b60f635~tplv-t2oaga2asx-zoom-in-crop-mark:3024:0:0:0.awebp)

### 安装最新稳定版本

```
nvm install stable

```

这个根据时间而定，看个人需求使用

> 友情链接：[官方 GitHub](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fnvm-sh%2Fnvm "https&#x3A;//github.com/nvm-sh/nvm") 
>  [https://juejin.cn/post/6844903839204638734](https://juejin.cn/post/6844903839204638734)
