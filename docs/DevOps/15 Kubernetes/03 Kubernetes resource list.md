# 资源清单
## 资源清单分类
* 名称空间级别：kubeadm k8s kube-system(只在特定的命名空间下生效，默认为default)
    * 工作负载型资源（workload）: pod、replicaset、deployment、statefulset、daemonset、job、cronjob
    * 服务发现及负载均衡型资源(ServiceDiscovery Loadbalance):service、ingress
    * 配置与存储型资源: Volume(存储卷)、csi(容器接口，可以扩展各种各样的第三方存储卷)
    * 特殊类型的存储卷:configmap(当配置中心来使用的资源类型)、secert(保存敏感数据)、downwardapi(把外部环境中的信息输出给容器)
* 集群级别：role （全集群中都可见）
    * namespace、node、clusterrole、rolebinding、clusterrolebinding
* 元数据型：hpa （通过指标进行平滑扩展）
    * hap、podtemplate、limitrange

## Pod 生命周期

![image-20200606183815423](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200606183815423.png?image/auto-orient,1/quality,q_90)


> * 启动过程
>
>   * pause  create（用于容器网络存储等资源共享）,网络、数据卷启动
>
>   * initC 只是初始化操作完成后释放
>
>     * 可以有多个initC
>     * initC 容器总是运行到成功完为止
>     * 每个init容器都必须在下一个init容器启动之前成功完成
>     * 如果init失败会一直重启，如果Pod对应的restartPolicy为Nerver，它不会重新启动（默认为always）
>     * 对init容器修改image字段Pod会重新启动，修改其他字段无效。
>     * init 具有应用容器的所有字段，*readiness*、*liveness*除外
>     * 每个app 和init 名称必须是唯一的
>
>   * Main C 运行
>
>     * 容器运行之前和结束有 start 、stop 完成的指令操作
>
>     * 探针：**readiness**就绪检测、**liveness**生存检测（kubelet对容器的定期诊断）
>
>       ​	*Readiness 检测成功后容器的状态变更（可进行延迟检测,如5s后进行）*
>
>       * 三种类型处理程序
>         * ExecAction：在容器中执行指定命令。如果命令退出时返回码为0则认为诊断成功。
>         * TcpSocketAction：对指定端口上的容器的IP地址进行Tcp检查。如果端口打开，则诊断成功。
>         * HttpGetAction：对指定端口和路径上的容器IP地址执行Http Get请求。如果相应的状态码大于等于200且小于400，则诊断成功。
>       * 探针三种结果
>         * 成功：通过诊断
>         * 失败：未通过诊断
>         * 位置：诊断失败，不会采取任何行动
