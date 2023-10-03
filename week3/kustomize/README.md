
# kustomize

我的目录结构

```
overlays
        └── prod
            ├── data
            └── kustomization.yaml
```

在data中声明三个变量

```
OPTIONA=lsk
OPTIONB=strace
REDIS_HOST=redis
```

执行后查看是否成功,发现没有变化

```yaml
kubectl get -n dev cm vote-configmap -oyaml
apiVersion: v1
data:
  data: |+
    OPTIONA=lsk
    OPTIONB=strace
    REDIS_HOST=redis
```

![image-20231004002939063](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310040051371.png)

![image-20231004002955615](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310040051313.png)

> 进入容器查看env，发现效果不理想

```
data=OPTIONA=lsk
OPTIONB=strace
REDIS_HOST=redis
```

