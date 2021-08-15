* 加载内核模块

  ```bash
  [root@vpc kubeyml]# lsmod|grep ip_vs
  ip_vs_sh               16384  0
  ip_vs_wrr              16384  0
  ip_vs_rr               16384  0
  ip_vs                 147456  6 ip_vs_rr,ip_vs_sh,ip_vs_wrr
  nf_conntrack          114688  9 ip_vs,nf_nat,nf_nat_ipv4,nf_nat_ipv6,xt_conntrack,nf_nat_masquerade_ipv4,nf_conntrack_netlink,nf_conntrack_ipv4,nf_conntrack_ipv6
  libcrc32c              16384  1 ip_vs
  ```

  * 如果没有则加载

    ```bash
    modprobe -- ip_vs
    modprobe -- ip_vs_rr
    modprobe -- ip_vs_wrr
    modprobe -- ip_vs_sh
    modprobe -- nf_conntrack_ipv4
    ```

* 更改kube-proxy配置

  ```bash
  kubectl edit configmap kube-proxy -n kube-system
  ```

  * 找到如下部分，将mode中添加ipvs（源为空）

   ```bash
  kind: KubeProxyConfiguration
  metricsBindAddress: ""
  mode: ""
  nodePortAddresses: null
   ```

* 删除所有kube-proxy的pod

  ```bash
  kubectl delete pod kube-proxy-x4xls -n kube-system # 删除旧的
  kubectl get pod -n kube-system  # 查看新的
  ```

* 查看新的kube-proxy Pod日志，其中第二行显示Using ipvs Proxier表示启用，此时查看ipvsadm规则已写入

  ```bash
  [root@vpc kubeyml]# kubectl logs kube-proxy-w75f6 -n kube-system
  I0607 16:34:59.370458       1 node.go:136] Successfully retrieved node IP: 172.19.5.110
  I0607 16:34:59.370513       1 server_others.go:259] Using ipvs Proxier.
  W0607 16:34:59.371028       1 proxier.go:429] IPVS scheduler not specified, use rr by default
  I0607 16:34:59.371303       1 server.go:583] Version: v1.18.0
  I0607 16:34:59.371657       1 conntrack.go:52] Setting nf_conntrack_max to 131072
  I0607 16:34:59.373386       1 config.go:315] Starting service config controller
  I0607 16:34:59.373407       1 shared_informer.go:223] Waiting for caches to sync for service config
  I0607 16:34:59.375054       1 config.go:133] Starting endpoints config controller
  I0607 16:34:59.375073       1 shared_informer.go:223] Waiting for caches to sync for endpoints config
  I0607 16:34:59.473612       1 shared_informer.go:230] Caches are synced for service config
  I0607 16:34:59.477833       1 shared_informer.go:230] Caches are synced for endpoints config
  ```

  



