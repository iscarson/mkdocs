*  概念

  ```mermaid
  graph TB
  client(client) --> svc(https 443 Svc)
  svc(https 443 Svc) --> frontend1[v1]
  svc(https 443 Svc) --> frontend2[v2]
  svc(https 443 Svc) --> frontend3[v3]
  deploy(Deployment)--> frontend1[v1]
  deploy(Deployment)--> frontend2[v2]
  deploy(Deployment)--> frontend3[v3]
  ```

  > * 有且只有一个轮询 RR
  > * 只提供4层负载均衡，可以通过ingress的方式添加7层代理

* 四种类型

  > * **ClusterIP**：默认类型，自动分配一个仅Cluster内部可以访问的虚拟IP
  >
  >   * apiserver:用户通过kubectl发送创建service的命令，apiserver接收到请求后数据存储到etcd中（kube-proxy监测etcd中发生的变化）
  >
  >   * Kube-proxy: 每一个node节点都有一个kube-proxy进程负责感知service、pod的变化，并将信息写入到iptables/ipvs规则中
  >
  >   * Iptables/ipvs: 使用nat等技术将virtualIP流量转发到endpoint中
  >
  >   * **handlessService(无头服务)**：不需要负载均衡及service ip时，可以使用clusterIP（spec.clusterIP）的值为"None"来创建handless Service，这类服务不会分配clusterIP，kube-proxy不会处理他们，不会提供负载均衡和路由。
  >
  >     使用```dig -t -A myapp.default.svc.cluster.local. @10.96.0.10```A记录来解析查看效果
  >
  > * **NodePort**：在ClusterIP基础上为Service在每台机器上绑定一个端口，这样就可以通过NodeIP:NodePort来访问该服务
  >
  >   * 查询流程：```iptables -t nat -nvL```***KUBE-NODEPORTS***
  >
  > * **LoadBalancer**：在NodePort的基础上，借助cloud provider创建一个外部负载均衡器，并将请求转发到NodeIP:NodePort
  >
  >   * 供应商提供LAAS
  >
  > * **ExternalName**：把集群外部的服务引入到集群内部来，在集群内部直接使用。没有任何类型代理被创建，只有kubernetes1.7阔者更高版本的kube-dns才支持
  >
  >   **service返回CNAME和它的值，可以将服务映射到externalName字段的内容（如：hub.liangml.com）,externalName service 是service的特例，他没有selector，也没有定义任何的端口和Endpoint。相反的，对于运行在集群外部的服务，它通过返回外部服务的别名这种方式来提供服务**
  >
  >   ```bash
  >   kind: Service
  >   apiVersion: v1
  >   metadata:
  >     name: my-service-1
  >     namespace: default
  >   spec:
  >   	type: ExternalName
  >   	externalName: hub.liangml.com
  >   ```
  >
  >   **当查询主机 my-service-1.default.svc.cluster.local（SVC_NAME.NAMESPACE.svc.cluster.local）时，集群的DNS服务将返回一个值 hub.liangml.com的CNAME的记录。访问这个服务的工作方式和其他相同，唯一不同的是重定向发生在DNS层，而不会进行代理或转发**
  >
  > * **Ingress-nginx**
  >
  >   ![image-20200608013757704](/Users/liangml/Library/Mobile Documents/com~apple~CloudDocs/Typora/Timage/image-20200608013757704.png)
  >
  >   ![image-20200608014838668](/Users/liangml/Library/Mobile Documents/com~apple~CloudDocs/Typora/Timage//image-20200608014838668.png)
  >
  >   * **使用NodePort方式暴露访问，后端需要创建Svc**
  >
  >   * ingress-nginx
  >
  >     * 网址
  >       * [官方网站](https://kubernetes.github.io/ingress-nginx)
  >       * [GitHub地址](https://github.com/kubernetes/ingress-nginx)
  >
  >     * http代理
  >
  >     * https代理
  >
  >       ```bash
  >       # 创建证书cert存储方式
  >       openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj"/CN=nginxsvc/0=nginxsvc"
  >       kubectl create secret tls pls-secret --key tls.key --cert tls.crt
  >       ```
  >
  >     * BasciAuth
  >
  >       ```bash
  >       yum -y install httpd # 需要使用apache模块实现
  >       htpasswd -c auth foo
  >       kubectl create secret generic basic-auth --from-file=auth
  >       ```
  >
  >     * nginx重写
  >
  >       | 名称                                           | 描述                                                      | 值   |
  >       | ---------------------------------------------- | --------------------------------------------------------- | ---- |
  >       | nginx.ingress.kubernetes.io/rewrite-target     | 必须重定向流量的目标Url                                   | 串   |
  >       | nginx.ingress.kubernetes.io/ssl-redirect       | 指示位置部分是否仅可访问SSL（当ingress包含证书时为True）  | 布尔 |
  >       | nginx.ingress.kubernetes.io/force-ssl-redirect | 即使ingress未启用TLS，也强制重定向到HTTPS                 | 布尔 |
  >       | nginx.ingress.kubernetes.io/app-root           | 定义Controller必须重定向到应用程序根，如果它在'/'上下问中 | 串   |
  >       | nginx.ingress.kubernetes.io/use-regex          | 指定ingress上定义的路径是否使用正则表达式                 | 布尔 |

* svc组件的访问规则

![image-20200607224543293](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200607224543293.png?image/auto-orient,1/quality,q_90)


* Vip 和 Service 代理

  > * 每个node运行一个kube-proxy进程
  > * Kube-proxy负责为service 实现虚拟VIP的，而不是externalName
  >   * K8s  1.0 使用userspace >> 1.1 版本中新增了iptables（1.2版本默认为iptables代理）>> 1.8-bata.0中添加了ipvs代理（1.14开始默认使用）
  >   * 1.0版本为四层代理，1.1版本新增了ingress（beta版本）7层代理
  > * IPvs代理模式
  >   * 如果不满足以下模块的开启，将会降级到iptables代理
  >     * rr  轮询调度
  >     * lc 最小链接数
  >     * dh 目标哈希
  >     * sh 源哈希
  >     * sed 最短期望延迟
  >     * nq 不排队调度

