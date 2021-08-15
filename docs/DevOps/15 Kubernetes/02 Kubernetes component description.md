![image-20200605234402771](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200605234402771.png?image/auto-orient,1/quality,q_90)


**k8s高可用集群最好是>=3奇数个**

Master :（每个组件本地会生成缓存）

* apiserver 所有组件的访问入口
* scheduler >> api server >> etc   负责介绍任务，选择合适的节点分配任务
* replication controller  维护副本数目
* Etcd：键值对数据库  存储k8s的集群所有的重要信息（持久化）

node:

 * kubelet  与CRI 交互操作docker容器维持生命周期

 * Kube proxy  负载  pod之间访问   默认操作防火墙新版本支持ipvs

 * Docker 

 * Pod

    * **pause**只要启动就会有一个容器启动，提供容器之前的通讯、共享

    * 控制器类型

       * ReplicationController（rc） && Replicaset（Rs）&& deployment

         > 解释：
         >
         > * HPA（HorizontallPodAutoScale）仅适用于Deployment和ReplicaSet，在v1版本中仅支持根据Pod Cpu使用率扩容，在v1alpha版本中，支持根据内存和用户自定义的metric扩容
         >
         > * ReplicationController 用来确保容器应用的副本数始终保持在用户定义的副本数，即如果有容器异常退出，会自动创建新的Pod来代替，而如果异常多出来的容器也会自动回收。**在新版本的kubernetes中建议使用ReplicaSet来取代ReplicationControlle**
         > * ReplicaSet跟ReplicationController没有本质的不同，只是名字不一样，并且ReplicaSet支持集合式的selector
         > * 虽然ReplicaSet可以独立使用，但一般还是建议使用deployment来自动管理ReplicaSet，这样就无需担心跟其他机制的不兼容问题（比如ReplicaSet不支持rolling-update但Deployment支持）

         * Deployment原理

           ```mermaid
           graph TB
           A(Deployment) --> |create| B[ReplicaSet  RC]
           A(Deployment) --> |create| C[ReplicaSet  RC]
           B[ReplicaSet  RC]--> |create| D[Pod]
           B[ReplicaSet  RC]--> |create| E[Pod]
           ```

          * HPA原理

            ```mermaid
            graph TB
            A(HPA cpu > 80/Max 10/Min 2) -->|monitor| B[RS]
            B[RS] --> C[v2]
            B[RS] --> D[v2]
            ```

       * Statefullset 

         > * 稳定的持久化存储，即Pod重新调度后还是能访问到相同的持久化数据，基于PVC来实现
         > * 稳定的网络标志，即Pod重新调度后其PodName和HostName不变，基于Headless Service（即没有Cluster IP 的 Service）来实现
         > * 有序部署，有序扩展，即Pod是有顺序的，在部署或者扩展的时候要依据定义的顺序依次进行（即从0到N-1，在下一个Pod运行之前所有之前到Pod必须是Running和Ready状态），基于init containers来实现
         > * 有序收缩，有序删除（即从N-1到0）

       * Daemonset 

         > * 运行集群存储daemon，例如在每个Node上运行glistered、ceph
         > * 在每个Node上运行日志搜集daemon，例如fluent、logstash
         > * 在每个Node上运行监控daemon，例如Prometheus Node Exporter

       * Job 、Cronjob

         > Job 负责批处理任务，即仅执行一次的任务，他保证批处理任务的一个或多个Pod成功结束
         >
         > Cron Job 管理基于时间的Job，即：
         >
         > * 在给定时间点只运行一次
         > * 周期性地在给定时间点运行

   * 服务发现 service （用于客户端访问一组Pod入口）

     * 具有相同点，如同一个Rs、Rc、Deployment、或者拥有同一组标签
     * 拥有自己的IP:Port
     * 使用RR轮询的方式存在

Coredns :可以为集群中的svc创建一个域名IP的对应解析关系

Ingress controller：官方实现四层代理，ingress 实现七层代理

Prometheus ：提供k8s集群监控

Dashboard ：给k8s集群提供一个B/S结构访问

Federation ：提供一个可以跨集群中心多个k8s统一管理功能

EFK：提供k8s集群日志统一分析介入平台

