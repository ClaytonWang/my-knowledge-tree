---
permalink: /devops/Mac OS 常用 Terminal Proxy 方法.html
---

# Mac OS 常用 Terminal Proxy 方法
`0. 首先配置好自己的本地 sock5 监听地址`

`1. 打开 .zshrc`

```
$ vi ~/.zshrc 

```

`2. 增加 proxy | unproxy`

```
alias proxy="
    export http_proxy=socks5://127.0.0.1:1086;
    export https_proxy=socks5://127.0.0.1:1086;
    export all_proxy=socks5://127.0.0.1:1086;
    export no_proxy=socks5://127.0.0.1:1086;
    export HTTP_PROXY=socks5://127.0.0.1:1086;
    export HTTPS_PROXY=socks5://127.0.0.1:1086;
    export ALL_PROXY=socks5://127.0.0.1:1086;
    export NO_PROXY=socks5://127.0.0.1:1086;"
alias unproxy="
    unset http_proxy;
    unset https_proxy;
    unset all_proxy;
    unset no_proxy;
    unset HTTP_PROXY;
    unset HTTPS_PROXY;
    unset ALL_PROXY;
    unset NO_PROXY"

```

`3. 使用 .zshrc`

```
$ source ~/.zshrc 

```

`4. 测试 proxy 效果`

```
$ curl https://ip.cn
>> {"country": "广东省深圳市", "city": "电信"}
$ proxy
$ curl https://ip.cn # 注意不要 sudo
>> {"country": "美国", "city": "Choopa"}

```

 [https://juejin.cn/post/6844903895550935053](https://juejin.cn/post/6844903895550935053)
