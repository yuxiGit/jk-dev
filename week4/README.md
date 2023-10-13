# GitOps案例

首先到`demo-8/crossplane/tencent`下，使用`terraform`创建出基础环境

![image-20231013104717096](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606948.png)

```
#添加argo仓库
helm repo add argo https://argoproj.github.io/argo-helm
#安装argo
helm install argocd argo/argo-cd -n argocd --create-namespace
#给argo配置git仓库，腾讯云provider
kubectl apply -f demo-9/setup/
#获取argo密码
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
#暴露argo端口
kubectl port-forward service/argocd-server -n argocd 8080:80
```

![image-20231013140654438](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606950.png)

#GitOps创建redis

最开始一直失败，提示`type`和`typeId`只能有一个,网络相关配置也是用的cvm的配置，

```yaml
vpcidRef:
      name: "example-cvm-vpc"
    subnetIdRef:
      name: "example-cvm-subnet"
```

一直修改尝试，只配置`typeId`结果偶然创建成功，发现提示已经创建，但是同步失败，腾讯云也可以看到有创建成功

![image-20231013140033286](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606951.png)

![image-20231013145208134](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606952.png)

![image-20231013145334728](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606953.png)

![image-20231013145316853](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606954.png)



在argo界面尝试sync，选择replace尝试失败

![image-20231013145542190](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606955.png)

通过`argo`删除`redis`，然后再次同步

提示网络白名单什么的，直接创建新的`vpc`，还是不行

==看官方文档发现，需要指定`vpcid`,否则默认使用基础网络==

![image-20231013151144183](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606956.png)

修改yaml，使用的`vpcId`和`subnetId`（给redis创建的vpc和子网）是在腾讯云拿到后，硬编码

```yaml
apiVersion: redis.tencentcloud.crossplane.io/v1alpha1
kind: Instance
metadata:
    name: redis-example
spec:
  forProvider:
    name: "redis-crossplane-example"
    availabilityZone: "ap-hongkong-2"
    memSize: 2048
    typeId: 8
    passwordSecretRef:
      name: "redis"
      key: "password"
      namespace: "crossplane-system"
    vpcId: vpc-eys56o52
    subnetId: subnet-lvtjw1lz
```

再次同步，成功

![image-20231013154528061](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606957.png)

![image-20231013154538035](https://raw.githubusercontent.com/Cleveryuxi/ImageBed/main/image/202310131606958.png)

在修改文件过程种，尝试过配置`noAuth`,当时也是提示`noAuth`为`true`时，需要配置`vpcId`和`subnetId`，但是配置后还是失败

[参考链接](https://cloud.tencent.com/document/product/239/20026#2.-.E8.BE.93.E5.85.A5.E5.8F.82.E6.95.B0)