# 配置多个git账号 - 掘金
> 为同一个电脑，配置多个 git 账号，其整体流程如下：
>
> -   清空默认的全局 `user.name` 和 `user.email`
> -   为不同的 `git` 账户生成不同的 `ssh-key`
> -   将以上的 `ssh-key` 分别添加到 `ssh-agent` 信任列表
> -   添加以上的公钥到自己的 `git` 账户中
> -   在 `config` 文件配置多个 `ssh-key`
> -   测试

```bash
git config --global --unset user.name
git config --global --unset user.email
```

> `id_ras` 是默认的文件名称，我们现在需要生成不同的 `ssh-key`，所以要设置不同的文件存储对应的公钥，比如：自己的 GitHub 账户，使用 `id_ras_github` 命名；公司的账户，使用 `id_ras_company` 来命名

```bash
ssh-keygen -t id_ras_github -C "xxx@xx.com"
ssh-keygen -t id_ras_company -C "xxx@company.com"
```

```javascript
ssh-add ~/.ssh/id_ras_github
ssh-add ~/.ssh/id_ras_company
```

> 如果看到 `Identitiy added: ~/.ssh/id_ras_github`，就表示添加成功了。

> 使用命令，copy 公钥，到 git 账户中粘贴即可。

```javascript
pbcopy < ~/.ssh/id_ras_github.pub
```

```javascript
pbcopy < ~/.ssh/id_ras_company.pub
```

> 在 .ssh/ 目录下，config 文件（没有的话新建一个）

```bash

Host company.gitee.com
Hostname gitee.com
IdentityFile ~/.ssh/id_rsa_github
User company
  

Host me.gitee.com
Hostname gitee.com
IdentityFile ~/.ssh/id_rsa_company
User me
```

> 测试：ssh -T git@{config 里面的 user}.xxx 主机名

```css
ssh -T git@company.gitee.com
```

> 出现以下，则说明成功！

```vbnet
Hi user.name！ You
```

> 在不同的代码仓库进行代码提交时，记得检查用户名和邮箱，以免混淆。设置用户名和邮箱的命令如下：

```bash
git config --local user.name xxx
git config --local user.email xxx@xxx.com
```

 [https://juejin.cn/post/6999630383130083358](https://juejin.cn/post/6999630383130083358)
