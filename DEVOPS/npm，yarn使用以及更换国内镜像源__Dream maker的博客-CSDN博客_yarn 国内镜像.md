# npm，yarn使用以及更换国内镜像源__Dream maker的博客-CSDN博客_yarn 国内镜像
## 原淘宝 npm 域名即将停止解析！！使用老域名的请尽快更新到新域名！

> [《淘宝 NPM 镜像站喊你切换新域名啦》](https://zhuanlan.zhihu.com/p/430580607) [https://npm.taobao.org](https://npm.taobao.org) 和 [https://registry.npm.taobao.org](https://registry.npm.taobao.org) 将在 2022.06.30 号正式下线和停止 DNS 解析。

域名切换规则：

[https://npm.taobao.org](https://npm.taobao.org/) => [https://npmmirror.com](https://npmmirror.com/)  
[https://registry.npm.taobao.org](https://registry.npm.taobao.org/) => [https://registry.npmmirror.com](https://registry.npmmirror.com/)

* * *

#### 常用命令：

| 功能 / 工具名称    | yarn                | npm            |
| ------------ | ------------------- | -------------- |
| 安装全部依赖       | yarn / yarn install | npm install    |
| 添加一个 / 多个依赖  | yarn add xx         | npm install xx |
| 删除一个 / 多个依赖  | yarn remove         | npm uninstall  |
| 搜索 package 包 | /                   | npm search     |

#### [yarn](https://so.csdn.net/so/search?q=yarn&spm=1001.2101.3001.7020)：

> Yarn 是由 Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具 。

##### 安装：

> npm install -g yarn

执行 `yarn xxx` 命令报以下错误时：  
![](https://img-blog.csdnimg.cn/1859df034ea54a6dbe0559f5bd65f6d1.png)

操作以下步骤进行策略更改：

> 1.  使用管理员身份运行 cmd 或 Power shell
> 2.  输入：set-executionpolicy remotesigned
> 3.  输入”Y“，回车保存

##### yarn 配置源：

```js

yarn config get registry


yarn config set registry https://registry.npmmirror.com


yarn config set registry https://registry.yarnpkg.com


yarn config delete registry

```

#### npm 配置源：

> 注意 npm 更换国内镜像源之后，将无法再使用 `npm search` 命令，需要恢复为官方源才可以使用，如果恢复官方源后还不可使用，运行删除注册表命令后重试即可。

```js

npm config get registry


npm config set registry https://registry.npmmirror.com


npm config set registry https://registry.npmjs.org


npm config delete registry

```

如果对你有帮助，来一个点赞和收藏吧 ｡◕‿◕｡ 
 [https://blog.csdn.net/qq_36651686/article/details/116536156](https://blog.csdn.net/qq_36651686/article/details/116536156)
