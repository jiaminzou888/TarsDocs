# TarsK8S 快速安装

## 在 K8S 上安装 TARS 框架

直接使用官方提供的镜像完成安装! helm 请使用 helm3 版本.

- 添加 helm 仓库

```
helm repo add tars-k8s https://tarscloud.github.io/K8SFramework/charts
```

- 安装控制器

```
helm install tarscontroller tars-k8s/tarscontroller
```

- 在 k8s 中创建访问仓库的 secret

**tars-image-secret 这个名字不要改, helm 包等都直接引用这个 secret 名称, 最好不要修改!**

```
kubectl create secret docker-registry tars-image-secret -n tars-dev --docker-server=${docker_registry} --docker-username=${docker_user} --docker-password=${docker_pass}
```

- 创建 TARS 环境

```
kubectl create ns tars-dev

helm install tarsframework -n tars-dev --set 'dockerRegistry=tarscloud,web=${web_host}' tars-k8s/tarsframework

```

参数说明:

- tars-dev: 这个是 K8S 上的名字空间 , 表示 Tars 部署在这个名字空间内
- web_host: 访问 tars web 的地址, 注意集群中必须已经按了 ingress, 且 web_host 指向了 ingress 的入口!

## 升级说明

如果是升级, 方式类似, 使用 helm upgrade 命令即可, 比如:

```
helm upgrade tarscontroller --set 'helm.build.id=v1.0.0-nightly' tars-k8s/tarscontroller
helm upgrade tarsframework -n tars-dev --set 'dockerRegistry=tarscloud,web=${web_host},helm.build.id=v1.0.0-nightly' tars-k8s/tarsframework

```

注意最好只升级版本, 不要降级版本, 同时注意升级版本需要自己执行 CRD(crd 如果变可以不管)

但是注意: 如果是升级, 最好手动执行 crd!!!当然 CRD 通常不会随便升级, 但是如果大版本升级, CRD 通常会升级.

```
cd install/tarscontroller/crds
#应用所有的yaml
kubectl apply -f ...

```

说明:

- helm.build.id: 对应了 K8SFramework 的 tag 版本号!

## 调度

完成以上步骤以后, 实际框架并没有在 K8S 上调度起来, 请参考[框架调度](./framework-affinity.md)