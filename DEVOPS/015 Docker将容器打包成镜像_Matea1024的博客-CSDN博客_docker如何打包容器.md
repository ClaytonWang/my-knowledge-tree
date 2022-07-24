# 015 Docker将容器打包成镜像_Matea1024的博客-CSDN博客_docker如何打包容器
-   [https://www.runoob.com/docker/docker-commit-command.html](https://www.runoob.com/docker/docker-commit-command.html)

## [docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020) commit : 从容器创建一个新的镜像。

```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

```

#### OPTIONS 说明：

-   \-a : 提交的镜像作者；
-   \-c : 使用 Dockerfile 指令来创建镜像；
-   \-m : 提交时的说明文字；
-   \-p : 在 commit 时，将容器暂停。

**例 1**

```
docker commit -a "runoob.com" -m "my apache" a404c6c174a2  mymysql:v1 

```

**例 2**

```
docker commit -m  ""   -a  ""   [CONTAINER ID]  [给新的镜像命名]

docker commit -m  ""   -a  "" aa myelasticsearch:1.0


```

 [https://blog.csdn.net/HuanFengZhiQiu/article/details/94412135](https://blog.csdn.net/HuanFengZhiQiu/article/details/94412135)
