
# 集群安装

## 配置主机名及hosts
```bash
hostnamectl set-hostname k8s-master01
```
## 安装依赖包
```bash
yum install -y conntrack ntpdate ntp ipavsadm ipset jq iptables curl sysstat libseccomp wget vim net-tools git
```
## 设置防火墙为iptables并设置空规则
```bash
systemctl stop firewalld &&systemctl disable firewalld 
yum -y install iptables-services && systemctl start iptables && systemctl enable iptables && iptables -F &&
service iptables save
```
## 关闭selinux
```bash
swapoff -a && sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab(测试未生效)
```
## 调整内核参数，对于k8s
```bash
cat > kubernetes.conf <<EOF
#开启网桥模式
net.bridge.bridge-nf-call-iptables = 1 
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward=1
net.ipv4.tcp_tw_recycle=0
# 禁止使用swap空间只有当系统oom时才使用它
vm.swappiness=0 
# 不检查物理内存是否够用
vm.overcommit_memory=1 
# 开启oom
vm.panic_on_oom=0 
fs.inotify.max_user_instances=8192
fs.inotify.max_user_watches=1048576
fs.file-max=52706963
fs.nr_open=52706963
# 关闭ipv6协议
net.ipv6.conf.all.disable_ipv6=1 net.netfilter.nf_conntrack_max=2310270
EOF
cp kubernetes.conf /etc/sysctl.d/kubernetes.conf
sysctl -p /etc/sysctl.d/kubernetes.conf
```
## 调整系统时区
```shell
# 设置系统时区为中国/上海
timedatectl set-timezone Asia/Shanghai
# 将当前的UTC时间写入硬件时钟
timedatectl set-local-rtc 0
# 重启依赖于系统时间的服务
systemctl restart rsyslog
systemctl restart crond
```
## 关闭系统不需要的服务
```shell
systemctl stop postfix && systemctl disable postfix
```
## 设置rsyslogd和systemd journald
```shell
mkdir /var/log/journal # 持久化保存日志的目录
mkdir /etc/systemd/journald.conf.d
cat > /etc/systemd/journald.conf.d/99-prophet.conf << EOF
[Journal]
# 持久化保存到磁盘
Storage=persistent
# 压缩历史日志
SyncIntervalSec=5m
RateLimitInterval=30s
RateLimitBurst=1000
# 最大空间占用10G
SystemMaxUse=10G
# 单日志文件最大 200M
SystemMaxFileSize=200M
# 日志保存时间2周
MaxRetentionSec=2week
# 不将日志转发到syslog
ForwardToSyslog=no
EOF
systemctl restart systemd-journald.service
```
## 升级系统内核为4.44
```shell
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
# 安装完成后检查 /boot/grub2/grub.cfg 中对应内核menuentry 中是否包含initrd16配置，如果没有，在安装一次！
yum --enablerepo=elrepo-kernel install -y kernel-lt
# 设置开机从新内核启动
grub2-set-default "CentOS Linux (4.4.226-1.el7.elrepo.x86_64) 7 (Core)"
```
## kube-proxy 开启ipvs的前置条件
```shell
modprobe br_netfilter
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```
## 安装docker软件
```bash
# step 1: 安装必要的一些系统工具
yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3: 更新并安装Docker-CE
yum makecache fast
yum -y install docker-ce
# Step 4: 开启Docker服务
service docker start

tee /etc/docker/daemon.json <<-'EOF'
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "registry-mirrors": ["https://ribhvzyz.mirror.aliyuncs.com"],
  "insecure-registries": ["https://xxx.com"]
}
EOF
mkdir -p /etc/systemd/system/docker.service.d
systemctl daemon-reload && systemctl restart docker && systemctl enable docker
```
## kubeadm(主从配置)
```bash
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
setenforce 0
yum install -y kubelet kubeadm kubectl
systemctl enable kubelet && systemctl start kubelet
```
## 初始化主节点
```bash
kubeadm config print init-defaults > kubeadm-config.yaml
    localAPIEndpoint:
        advertiseAddress: 172.19.5.110
    kubernetesVersion: v1.18.0
    networking:
        dnsDomain: cluster.local
        podSubnet: "10.244.0.0/16"
        serviceSubnet: 10.96.0.0/12
    ---
    apiVersion: kubeadm.k8s.io/v1beta2
    kind: KubeProxyConfiguration
    featureGates:
        SupportIPVSProxyMode: true
    mode: ipvs
    kubeadm init --config=kubeadm-config.yaml --upload-certs | tee kubeadm-init.log 
```
### 初始化完之后
#### node
```bash
[root@vpc ~]# docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
k8s.gcr.io/kube-proxy                v1.18.0             43940c34f24f        2 months ago        117MB
k8s.gcr.io/kube-apiserver            v1.18.0             74060cea7f70        2 months ago        173MB
k8s.gcr.io/kube-controller-manager   v1.18.0             d3e55153f52f        2 months ago        162MB
k8s.gcr.io/kube-scheduler            v1.18.0             a31f78c7c8ce        2 months ago        95.3MB
k8s.gcr.io/pause                     3.2                 80d28bedfe5d        3 months ago        683kB
k8s.gcr.io/coredns                   1.6.7               67da37a9a360        4 months ago        43.8MB
k8s.gcr.io/etcd                      3.4.3-0             303ce5db0e90        7 months ago        288MB
```
#### pod
```bash
[root@vpc ~]# kubectl get pod -n kube-system
NAME                          READY   STATUS    RESTARTS   AGE
coredns-66bff467f8-dkhbb      0/1     Pending   0          14m
coredns-66bff467f8-vrnpp      0/1     Pending   0          14m
etcd-vpc                      1/1     Running   0          14m
kube-apiserver-vpc            1/1     Running   0          14m
kube-controller-manager-vpc   1/1     Running   0          14m
kube-proxy-x4xls              1/1     Running   0          14m
kube-scheduler-vpc            1/1     Running   0          14m
```
## 部署网络
```bash
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl create -f kube-flannel.yml
kubectl get pod -n kube-system (查看flannel是否成功)

## 此时k8s node节点状态变为了 Ready
[root@vpc flannel]# kubectl get node
NAME   STATUS   ROLES    AGE   VERSION
vpc    Ready    master   21m   v1.18.3
```
## 问题
```shell
sysctl: cannot stat /proc/sys/net/bridge/bridge-nf-call-iptables: 没有那个文件或目录
modprobe br_netfilter
```