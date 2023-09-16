制作多平台镜像

准备好Dockerfile后，执行命令报错，执行环境ubuntu，docker 24.0.6。

```
Multiple platforms feature is currently not supported for docker driver. Please switch to a different driver (eg. "docker buildx create --use")
```

[docker](https://docs.docker.com/build/building/multi-platform/)官方文档这里有相关介绍，按照文档执行命令`docker buildx create --use --name mybuild node-amd64 mybuild`,这个命令会下载一个buildkit的镜像，然后起一个容器。

**这个问题随后要看一下**

然后在执行命令`sudo docker buildx build --platform linux/amd64,linux/arm64 -f Dockerfile . -t memexyx/test:v1 --push `就可以正常构建。

![image-20230916134951598](https://cdn.jsdelivr.net/gh/Cleveryuxi/ImageBed/image/202309161352987.png)