---
title: docker安装教程
date: 2021-08-30T15:55:42.822Z
description: docker安装教程
---
## 配置镜像代理

参考：[https://docs.docker.com/config/daemon/systemd/](https://docs.docker.com/config/daemon/systemd/)

如果你已经在本地windows上使用了客户端，默认可以通过它的1080端口来访问外网拉取镜像。在安装docker的linux服务器执行以下操作，其中192.168.0.103是你本地windows能上网的网卡IP：

在linux中启动

```
docker run -d --name clash-client \
  --restart always \
  -p 7890:7890 \
  -p 9090:9090 \

  willdockerhub/clash-ng
```

配置宿主机docker引擎

```
mkdir -p /etc/systemd/system/docker.service.d

cat > /etc/systemd/system/docker.service.d/http-proxy.conf  <<EOF
[Service]
Environment="HTTP_PROXY=http://192.168.92.1:7890"
Environment="HTTPS_PROXY=http://192.168.92.1:7890"
Environment="NO_PROXY=localhost,127.0.0.1,192.168.92.*,*.aliyuncs.com"
EOF

systemctl daemon-reload && systemctl restart docker

systemctl show --property=Environment docker
```

注意还要在客户端里鼠标右键勾选允许其他设备连接。以上配置完成后即可直接拉取国外网站镜像

```
docker pull k8s.gcr.io/kube-apiserver:v1.16.1
```

## 国内镜像加速

**阿里云镜像加速**

[https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

```
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://uyah70su.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload && sudo systemctl restart docker
```

**daocloud镜像加速**

[https://www.daocloud.io/mirror]()

```
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
systemctl restart docker.service
```

**华为云dockerhub镜像加速**

[https://console.huaweicloud.com/swr，镜像中心](https://console.huaweicloud.com/swr%EF%BC%8C%E9%95%9C%E5%83%8F%E4%B8%AD%E5%BF%83)

```
tee /etc/docker/daemon.json <<- 'EOF'
{
    "registry-mirrors": ["https://7bafc985f90c43b887a96c2b846cf984.mirror.swr.myhuaweicloud.com"]
}
EOF
systemctl daemon-reload && systemctl restart docker
```

**然后重新启动 Docker 服务：**

```
sudo systemctl daemon-reload && sudo systemctl restart docker
```

**检查加速器是否生效**

执行 $ docker info，如果从结果中看到了如下内容，说明配置成功。

```bash
$ docker info | grep Mirrors -A1
Registry Mirrors:
 https://uyah70su.mirror.aliyuncs.com/

#测试镜像拉取速度
$ time docker pull centos
```

直接加速

```
docker pull uyah70su.mirror.aliyuncs.com/library/python:3.6
```

## 阿里云镜像仓库

可以代理k8s.gcr.io镜像

```bash
#示例
docker pull k8s.gcr.io/pause:3.2

#改为
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause:3.2
```

## dockerhub镜像仓库

可以代理k8s.gcr.io镜像

```
#示例
docker pull k8s.gcr.io/pause:3.1
docker pull k8s.gcr.io/kube-apiserver:v1.17.3

#改为
docker pull googlecontainersmirrors/pause:3.1
docker pull googlecontainersmirrors/kube-apiserver:v1.17.3
```

## 七牛云镜像仓库

可以代理quay.io镜像

```
#示例
docker pull quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.30.0

#改为
docker pull quay-mirror.qiniu.com/kubernetes-ingress-controller/nginx-ingress-controller:0.30.0
```

参考：

[https://www.cnblogs.com/xuxinkun/p/11025020.html](https://www.cnblogs.com/xuxinkun/p/11025020.html)

[https://github.com/veggiemonk/awesome-docker#registry](https://github.com/veggiemonk/awesome-docker#registry)

[https://blog.csdn.net/weixin\_30852451/article/details/95878509](https://blog.csdn.net/weixin_30852451/article/details/95878509)

## 镜像拉取工具

**docker-wrapper**

一个 Python 编写的工具脚本，可以替代系统的 Docker 命令，自动从 Azure 中国拉取镜像并自动 Tag 为目标镜像和删除 Azure 镜像，一气呵成。

项目地址：[https://github.com/silenceshell/docker\_wrapper](https://github.com/silenceshell/docker_wrapper)

**azk8spull**

一个 shell编写的工具脚本

项目地址：[https://github.com/xuxinkun/littleTools](https://github.com/xuxinkun/littleTools)

使用方法

```
curl -Lo /usr/local/bin/azk8spull https://github.com/xuxinkun/littleTools/releases/download/v1.0.0/azk8spull && chmod +x /usr/local/bin/azk8spull
```

参考：

[https://kubernetes.io/docs/setup/release/](https://kubernetes.io/docs/setup/release/)

[https://yuyicai.com/posts/k8s-bin-mirrors/](https://yuyicai.com/posts/k8s-bin-mirrors/)

[https://github.com/anjia0532/gcr.io\_mirror](https://github.com/anjia0532/gcr.io_mirror)

## 常用镜像仓库

DockerHub镜像仓库:

[https://hub.docker.com/](https://hub.docker.com/)

google镜像仓库：

[https://gcr.io](https://gcr.io/)

[http://gcr.io/google-containers/](http://gcr.io/google-containers/)

[https://gcr.io/google-containers/pause](https://gcr.io/google-containers/pause)

[http://gcr.io/kubernetes-helm/](http://gcr.io/kubernetes-helm/)

coreos镜像仓库：

[https://quay.io/repository/](https://quay.io/repository/)

elastic镜像仓库：
[https://www.docker.elastic.co/](https://www.docker.elastic.co/)

RedHat镜像仓库：

[https://access.redhat.com/containers](https://access.redhat.com/containers)

阿里云镜像仓库：

[https://cr.console.aliyun.com](https://cr.console.aliyun.com)

华为云镜像仓库：

[https://console.huaweicloud.com/swr](https://console.huaweicloud.com/swr)