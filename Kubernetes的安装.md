---
title: Kubernetes的安装
id: 162
date: 2024-12-03 17:09:59
auther: yutanbird
cover: 
excerpt: 基于kubeadm搭建的k8s集群，解决了诸多的网络问题
permalink: /archives/kubernetes-de-an-zhuang
categories:
 - linux
tags: 
 - 集群
 - k8s
---

# 介绍

![Kubernetes 组件](https://imagere.oss-cn-beijing.aliyuncs.com/imggongwin/kubernetes-cluster-architecture.svg)

上图是`kubernetes`的架构，CONTROL PLANE是master节点，负责管理，`Nodex`是工作节点，负责具体的工作。

-   `api-server`: `api`接口，所有组件、所有其他用户想操作这个集群都是通过这个接口进行请求以及认证。
-   `Scheduler`：调度器，监控所有节点的负载情况，然后进行调度。
-   `Controller Manager`：控制器管理器，负责监控所有资源的状态，并对错误情况做出响应
-   `etcd`：键值存储系统，存储集群所有资源的状态信息，控制器管理器也是从这里获取

# 安装

## 准备工作

假设目前有三台电脑，即三个节点，下面准备工作，如没特别备注，每个节点都需要执行相同的操作

1.   更新软件列表

```shell
yum update && yum upgrade  # 更新软件列表
```

2.   时间同步

```shell
yum install -y chrony
systemctl enable chronyd
timedatectl set-timezone Asia/Shanghai
systemctl restart chronyd
```

3.   关闭防火墙：也可以把需要的端口打开，任选一

```shell
# 1. 关闭防火墙
systemctl stop firewalld.service    # 关闭
systemctl disable firewalld.service # 关闭开机自启
# 2. 开启端口
firewall-cmd --permanent --add-port=6443/tcp  # master必须开启
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10253/tcp
firewall-cmd --permanent --add-port=10254/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd --reload
```

4.   关闭selinux：会跟k8s运行时产生冲突

```shell
setenforce 0  # 临时关闭
sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config  # 永久关闭
sestatus # 查看selinux的状态
```

5.   关闭交换内存：1.22版本之前不支持交换内存，建议关闭

```shell
swapoff -a  # 临时关闭
sed -i 's/.*swap.*/#&/' /etc/fstab  # 永久关闭
free -h # 查看swap是不是为0
```

6.   配置iptables规则，确保k8s网络通信正常

```shell
cat << EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sysctl --system # 重新加载配置，可以选择其他步骤结束再重新加载
```

7.   更改linux的模块加载

```shell
cat << EOF > /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
modprobe overlay
modprobe br_netfilter
sysctl --system # 重新加载配置，可以选择其他步骤结束再重新加载
lsmod | grep overlay # 查看是否加载成功
lsmod | grep br_netfilter # 查看是否加载成功
```

8.   更改hostname（三台节点分开操作）

```shell
# 为master，work1，work2节点都更改相应的hostname
hostnamectl set-hostname k8smaster && bash  # master节点
hostnamectl set-hostname k8swork1 && bash  # work1节点
hostnamectl set-hostname k8swork2 && bash  # work2节点
```

9.   配置hosts文件，可以通过主机名访问到ip地址

```shell
cat << EOF >> /etc/hosts
<master ip> k8smaster
<worker1 ip> k8sworker1
<worker2 ip> k8sworker2
EOF
# 相互ping一下，看看是否通了
```

10.   配置静态ip地址，防止ip地址变换导致找不到节点

```shell
# 假设该台机器需要配置的ip是：My_ip
My_ip=$(ifconfig | grep -A 1 'eno1' | tail -1 | awk '{print $2}')
```



## 网络代理

由于我们的服务器比较害羞，因此想要访问网络资源，需要找其他服务器帮忙，因此配置一下代理

1.   系统代理

```shell
# 设置代理
# 你已经知道我的ip了，请不要ddos我，我还有个ip是127.0.0.1，你可以ddos，因为我有负载均衡，欢迎来d
proxy_ip="192.168.199.212"
proxy_port="7890"
no_proxy="localhost,192.168.199.0/24,10.0.0.0/8"
cat > ~/.system_proxy <<EOF
#!/bin/bash
export http_proxy=http://${proxy_ip}:${proxy_port}
export https_proxy=http://${proxy_ip}:${proxy_port}
export ftp_proxy=http://${proxy_ip}:${proxy_port}
export no_proxy=${no_proxy}
EOF
chmod +x ~/.system_proxy
source ~/.system_proxy

env | grep proxy # 查看系统代理
```

2.   yum代理（可选）

```shell
echo "proxy=http://${proxy_ip}:${proxy_port}" >> /etc/yum.conf  # yum不一定需要代理，它会走系统代理的
```

3.   docker pull代理，可以先安装了docker再来

```shell
if [ ! -d /etc/systemd/system/docker.service.d ]; then
	mkdir -p /etc/systemd/system/docker.service.d
fi
cat > /etc/systemd/system/docker.service.d/http-proxy.conf <<EOF
[Service]
Environment="HTTP_PROXY=http://${proxy_ip}:${proxy_port}/"
Environment="HTTPS_PROXY=http://${proxy_ip}:${proxy_port}/"
Environment="NO_PROXY=${no_proxy}"
EOF
```

4.   docker build/run代理，可以先安装了docker再来

```shell
if [ ! -d ~/.docker ]; then
	mkdir -p ~/.docker
fi
cat > ~/.docker/config.json <<EOF
{
	"proxies": {
		"default": {
			"httpProxy": "http://${proxy_ip}:${proxy_port}",
			"httpsProxy": "http://${proxy_ip}:${proxy_port}",
			"noProxy": "${no_proxy}"
		}
	}
}
EOF
systemctl daemon-reload
```

5.   添加docker源，受限于害羞，可以选择国内的源，这里用的是官方源（配置了yum代理即可）

```shell
yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  # 阿里云的源，目前可用
```

6.   添加Kubernetes源，受限于害羞，可以选择国内的源，这里用的是官方源（配置了yum代理即可）

```shell
# 官方源
cat <<EOF | tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/
enabled=1
gpgcheck=1
gpgkey=https://pkgs.k8s.io/core:/stable:/v1.30/rpm/repodata/repomd.xml.key
EOF

# 阿里云源
cat <<EOF | tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```



## 软件安装

1.   容器软件（需要先配置docker源）

`Kubernetes`是基于容器技术的，所以需要有容器运行时（cri），比如`containerd`（推荐），`cri-o`，`cri-docker`。这里选择`containerd`，也是主流选择，官网：[https://containerd.io/docs/getting-started/](https://containerd.io/docs/getting-started/)，官方安装教程可看：[https://github.com/containerd/containerd/blob/main/docs/getting-started.md](https://github.com/containerd/containerd/blob/main/docs/getting-started.md)

```shell
yum install -y yum-utils device-mapper-persistent-data lvm2  # 安装其他依赖：设备和逻辑卷管理工具
yum install -y containerd.io 
systemctl status containerd # 查看 containerd 的状态
```

2.   安装runc

```shell
# 没有wget命令，自行安装，注意自己的版本，可以去GitHub看
# github链接：https://github.com/opencontainers/runc/releases
mkdir -p ~/Downloads && cd ~/Downloads
wget https://github.com/opencontainers/runc/releases/download/v1.2.0-rc.2/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc
runc -v # 查看是否安装成功
```

3.   安装cni

```shell
# 没有wget命令，自行安装，注意自己的版本，可以去GitHub看
# github链接：https://github.com/containernetworking/plugins/releases
cd ~/Downloads  # 没有就创建目录
wget https://github.com/containernetworking/plugins/releases/download/v1.5.1/cni-plugins-linux-amd64-v1.5.1.tgz
mkdir -p /opt/cni/bin && tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.5.1.tgz
```

4.   上面三步骤安装的另一种方法

可以选择下列方法来安装上面三个步骤

```shell
# 没有wget命令，自行安装，注意自己的版本，可以去GitHub看
# github链接：https://github.com/containerd/containerd/releases
cd ~/Downloads  # 没有就创建目录
wget https://github.com/containerd/containerd/releases/download/v1.6.35/cri-containerd-cni-1.6.35-linux-amd64.tar.gz
tar Cxzvf / cri-containerd-cni-1.6.35-linux-amd64.tar.gz
rm /cri*.txt
```

5.   kube三件套

-   `kubeadm`：创建和管理集群的工具
-   `kubelet`：节点的守护进程，用于接收`master`下发的任务，必需
-   `kubectl`：`api`交互的命令行工具

```shell
yum list kubeadm kubelet kubectl --showduplicates | sort -r  # 查看有哪些版本，并且排序显示
yum install -y kubeadm kubelet kubectl  # 也可以选择需要的版本安装，这里选择最新版，master以外的节点可以不装kubectl
```

6.   安装docker（其实不是很必要，这里安装的是docker的交互工具、镜像制作工具、镜像启动工具等而已）

```shell
yum install -y docker-ce docker-ce-cli docker-buildx-plugin docker-compose-plugin

# 下载docker-compose
curl -L https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
chmod 755 /usr/local/bin/docker-compose
docker-compose version
```

# 创建集群

## 容器配置

需要配置`	cgroup` 为 `systemd`，需要配置cri（默认`containerd`中是关闭的）

```shell
mv /etc/containerd/config.toml /etc/containerd/config.toml.bak
containerd config default > /etc/containerd/config.toml  # 生成默认配置文件

# 找到[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]并将其中的SystemdCgroup更改为true，或者用下面的命令来更改
sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml  
# 找到registry.k8s.io，更换代理，这里依然用的是registry.lank8s.cn
sed -i 's/registry.k8s.io/registry.lank8s.cn' /etc/containerd/config.toml
# 启动容器
systemctl start containerd
systemctl enable containerd
# journalctl -xeu containerd # 查看containerd的详细启动信息
```

## 集群配置

```shell
# 启动kubelet
# systemctl start containerd  # 先不启动，没有初始化就启动，可能会失败，也有可能会占用6443端口
systemctl enable --now containerd  # --now相当于现在就启动，上下两句命令的集合
journalctl -xeu kubelet # 查看kubelet的详细启动信息
```

## master初始化集群

**注意**：仅需要在master节点上面操作

```shell
kubeadm init --v=5  # --v=5是显示详细信息

# 或者

kubeadm config print init-defaults --component-configs KubeletConfiguration> <config file>
## 更改文件后
kubeadm init --config <config file>  # 从config file启动
```

关于`kubeadm`命令的参数，可以通过官网的[`kubeadm init`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-init/)查看，以及其他的`kubeadm`命令，也可以在侧边栏找到，包括[`kubeadm join`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-join/), [`kubeadm upgrade`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-upgrade/), [`kubeadm config`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-config/), [`kubeadm reset`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-reset/), [`kubeadm token`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-token/), [`kubeadm certs`](https://kubernetes.io/zh-cn/docs/reference/setup-tools/kubeadm/kubeadm-certs/)等。下面如果有遇到不熟悉的命令，请去官网看详细解释。

受限于网络问题，所以这里还是选择用上面的第二种方法，先生成配置文件

```shell
kubeadm config print init-defaults --component-configs KubeletConfiguration > kubernetes_init.yaml

# 1. 更改api的ip地址为自己的地址
sed -i 's/1.2.3.4/'${My_ip}'/g' kubeadm_init.yaml
# 2. 更改image镜像库
sed -i 's#registry.k8s.io#registry.lank8s.cn#g' kubeadm_init.yaml
# 3. 手动添加pod的ip网段
sed -i '/serviceSubnet/a \ \ podSubnet: 10.224.0.0/16' kubeadm_init.yaml
# 4. 更改节点名字
sed -i 's/name: node/name: init_node' kubeadm_init.yaml

# 除此之外，也可以改一下token等配置信息
# 值得注意的是：token必须满足： [a-z0-9]{6})\.([a-z0-9]{16}  也就是6ge小写字母或数字加上一个点再加上16个小写字母或数字
```

下面给一个完整的`yaml`文件

```yaml
apiVersion: kubeadm.k8s.io/v1beta3
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  advertiseAddress: 192.168.199.141  # 这里设置自己的ip
  bindPort: 6443
nodeRegistration:
  criSocket: unix:///var/run/containerd/containerd.sock
  imagePullPolicy: IfNotPresent
  name: k8smaster  # 节点文字改了，也可以改成自己的主机名
  taints: null
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta3
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns: {}
etcd:
  local:
    dataDir: /var/lib/etcd
imageRepository: registry.lank8s.cn  # 换源
kind: ClusterConfiguration
kubernetesVersion: 1.30.0
networking:
  dnsDomain: cluster.local
  serviceSubnet: 10.96.0.0/12
  podSubnet: 10.244.0.0/16  # 增加pod的网段
scheduler: {}
---
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: systemd  
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
containerRuntimeEndpoint: ""
cpuManagerReconcilePeriod: 0s
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMaximumGCAge: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging:
  flushFrequency: 0
  options:
    json:
      infoBufferSize: "0"
    text:
      infoBufferSize: "0"
  verbosity: 0
memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
```

接下来就可以初始化集群了

```shell
kubeadm init --config kubernetes_init.yaml --v=5  # --v=5: 防止出问题，我这里显示多一点信息
```

如果一切正常，最后会出现类似于以下的输出，没有的话，请看后面的常见问题

```shell
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.199.141:6443 --token a**********a \
        --discovery-token-ca-cert-hash sha256:9d234a****bae6d

```

如果没有成功，请先看后面的问题，然后再看这里跟着操作

```shell
# 如果是一般用户
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 如果是root用户，可以选择上一种方法，也可以直接索引到/etc/kubernetes/admin.conf就行，总之更加推荐上一种
export KUBECONFIG=/etc/kubernetes/admin.conf
```

如何检验是否运行成功呢？

```shell
kubectl get all # 查看当前所有的资源
```

### 初始化中常见的问题

说实话，前前后后我折腾了几天才初始化好master节点，主要还是对`kubernetes`不太熟悉，容器技术也是才学，搞不懂`runc`，`kube`三件套，`containerd`等等的区别，这里先给出层级和依赖关系。

```
# 用来编排，控制容器的，这个docker是狭义的命令行docker
docker  或者   kubernetes   

  |			       	|
  |					|
  |					|
  |	cri(容器运行时接口, container Runtime Interface)
  |					|
  |					|
containerd 或 cri-o 或 cri-docker  # 容器运行时
			|
			|
			|
			|
open container initiative(OCI规范)
			|
			|
			|
		   runc
		    |
		    |
		    |
		container实例
		
crictl      # 容器运行时的命令行交互接口

# containerd容器运行时依赖如下
containerd  # 本体
crt			# 容器运行时的另一个命令行交互接口，直接调用的containerd的api，适合底层调试，crictl是与CRI兼容的容器运行时用的命令行工具，主要用于kubernetes环境
cni			# 容器运行时的网络接口，containerd network interface

# cri, cni, csi的区别
cni(container network interface): 容器运行时网络接口
cri(container runtime interface): 容器运行时接口
csi(container storage interface): 容器运行时存储接口
```

下面给出常见的问题及解决思路

-   如果发生端口被占用以及文件已存在的

```shell
# 通常失败之后，请手动关闭kubelet并且删除一些文件
# 重新启动需要执行下列命令
systemctl stop kubelet  # 停止kubelet占用端口
rm -rf /etc/kubernetes/* # 删除配置文件重新来

# 如果上述方法不管用，采用reset命令
kubeadm reset --cleanup-tmp-dir  # 这个会连临时目录也删除
```

-   拉取镜像失败

```shell
[ERROR ImagePull]: failed to pull image registry.k8s.io/...
```

这通常是由于网络问题导致的，我发现即使设置了系统代理，`kubeadm`仍然不会使用，也许是又一次害羞了吧，它通常推荐了我们手动拉取镜像

```shell
kubeadm config images pull # 拉取镜像
kubeadm config images list # 查看有哪些镜像

# 可以看到有下列镜像
registry.k8s.io/kube-apiserver:v1.30.3
registry.k8s.io/kube-controller-manager:v1.30.3
registry.k8s.io/kube-scheduler:v1.30.3
registry.k8s.io/kube-proxy:v1.30.3
registry.k8s.io/coredns/coredns:v1.11.1
registry.k8s.io/pause:3.9
registry.k8s.io/etcd:3.5.12-0
```

解决方法：尝试换源，既然无法走代理。

```shell
kubeadm config images list --image-repository=<源> 
# 或者
kubeadm init --image-repository=<源>

# 下面列出一些源
# registry.aliyuncs.com/google_containers: 已经404了
# lank8s.cn: 个人的镜像，可能会比较慢，介绍看这，https://github.com/lank8s
	# registry.k8s.io —— registry.lank8s.cn
	# gcr.io —— gcr.lank8s.cn
# swr.cn-north-4.myhuaweicloud.com/ddn-k8s/registry.k8s.io: 

kubeadm init --image-repository=registry.lank8s.cn --v=5
```

-   kubelet中启动失败

```shell
# 通常出现
	- The kubelet is not running
	- The kubelet is unhealthy due to a misconfiguration of the node in some way (required cgroups disabled)

# 可以通过检查kubelet和containerd的日志来尝试解决

journalctl -xeu containerd  # 查看containerd的日志
journalctl -xeu kubelet     # 查看kubelet的日志
```

### 调试方法

```shell
# kubernetes使用crictl作为容器交互命令，而不是docker。crictl更适合k8s，还有一个是ctr是直接与containerd交互的
crictl images list  # 列出所有镜像

crictl ps # 查看目前运行了哪些镜像，kubeadm config images list # 列出的所有镜像都需要在运行中

# 假如运行失败kubeadm init，之后运行还需要--image-repository参数，因此可以选择tag一下目前的镜像，需要用ctr来进行tag
ctr namespace ls # 查看命名空间，应该只有k8s.io这一个
ctr -n <namespace> images ls # 查看命名空间下的镜像
ctr -n <namespace> i tag <image:tag> <newimage:newtag>
```

## worker节点加入集群

在master上面会输出token和hash，如果没有记住，可以在master上面重新查看一下

```shell
kubeadm token list  # 列出所有token

kubeadm token generate # 生成token
kubeadm token delete # 删除token


# 查看hash，命令有点长，来拆解一下
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | \
openssl rsa -pubin -outform der 2>/dev/null | \
openssl dgst -sha256 -hex 
# 1. openssl x509 -pubkey -in /etc/kubernetes/pki/ca.cert
# x509：x509证书管理，x.509证书是密码学中公钥证书的格式标准
# -pubkey 提取公钥信息
# -in 显然是ca证书输入文件了
# 所以第一句命令很明显了，就是从ca证书中提取公钥信息

# 2. openssl rsa -pubin -outform der 2>/dev/null
# rsa rsa加密
# -pubin 输入数据中有公钥
# -outform 后面跟的是输出格式，
# der和pem两种格式，der是二进制、pem是base64。
# pem包含公钥，私钥，证书等信息，通常以.pem结尾的文件
# der是二进制编码格式，用于x.509，crl等，以.der或.cer结尾
# 2>/dev/null 表示错误输出不显示，重定向到/dev/null了
# 所以这条命令，显然只是转换格式

# 3. openssl dgst -sha256 -hex
# dgst 摘要信息
# -sha256 使用sha256编码，k8s通常用这个
# -hex 十六进制显示


```

得到`token`和`sha256:hash`后，可以开始`join`了

```shell
kubeadm join <master ip>:<port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>   # 即可加入节点

# 比如我的
kubeadm join 192.168.199.141:6443 --token abcdef.0123456789abcdef --discovery-token-ca-cert-hash sha256:9d234af9b45ef25c3843252d0a7d5fbace165abeab8d93edb536f11d090bae6d

# 出现下面信息，就是加入成功
This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

# kubectl命令

详情参考官网[命令行工具(kubectl)](https://kubernetes.io/zh-cn/docs/reference/kubectl/)，而且是中文的，支持非常友好。

下面给出常用命令

```shell
kubectl get all -o wide  # 显示所有的资源，-o wide表示显示的信息更加广
kubectl get node -o wide # 显示所有的node，node不在上述命令显示，因为它是物理资源，不是逻辑资源
kubectl apply -f <yaml配置文件> # 根据配置文件，启动/更新一个资源，已有资源相同的话，就是更新这个资源
kubectl create -f <yaml配置文件> # 根据配置文件，新建一个资源，已有资源相同的话只会重复而不是更新

kubectl get namespace # 简写成kubectl get ns
```

# helm安装

Helm 是 Kubernetes 的包管理器

```shell
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```



# 在集群中运行一些实用软件

## Dashboard UI

官方的ui访问，但是只能在安装这个软件的机器上访问，不支持外部访问。

```shell
# 添加 kubernetes-dashboard 仓库
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
# 使用 kubernetes-dashboard Chart 部署名为 `kubernetes-dashboard` 的 Helm Release
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
```

## ingress-nginx

反向代理工具，处理集群内的流量，`nginx`是处理集群外的流量



## portainer

这也是一个ui访问，非官方的

```shell
curl -LO https://raw.githubusercontent.com/portainer/portainer-k8s/master/portainer.yaml
kubectl apply -f portainer.yaml
```























