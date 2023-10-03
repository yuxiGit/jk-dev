# helm

在做 registry-secret时参考了群里大佬的做法，当时首先想到的是通过文件，但是不知道文件的格式该怎样写，于是先`dry-run`看了一下格式，发现是加密的，然后就参考群里大佬的做法，太牛啦！！

把大佬的参考文档也写进来：

- 参考文档
  - https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/pull-image-private-registry/#log-in-to-docker-hub
  - https://cloud.tencent.com/developer/article/1985811

定义变量

```yaml
REGISTRY:
  auths:
    registry.cn-hangzhou.aliyuncs.com:
      username: xyx11332
      password: xyx11332..
      auth: eHl4MTEzMzI6eHl4NDU2Nzg5se==
      
```

渲染模板

```yaml
{{ $REGISTRY:= .Values.REGISTRY | toJson | b64enc}}
apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: null
  name: aliyun
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: {{ $REGISTRY }}
```

增加imagePullSecret配置

```yaml
spec:
      imagePullSecrets:
        - name: aliyun
      containers:
```

部署Chart

![image-20231003223605268](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310032236574.png)

![image-20231003223630031](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310032236112.png)