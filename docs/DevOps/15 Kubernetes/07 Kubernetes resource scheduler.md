# 调度说明

* 说明

  Scheduler 是k8s调度器，主要任务是把定义的Pod分配到集群节点上。Scheduler是作为单独的程序运行的，启动之后会一直监听API Server，获取PodSpec.NodeName为空的Pod，对每个pod都会创建一个binding，表示该Pod应该放到那个节点上

  * 公平：保证每个节点都能被分配到资源
  * 资源高效利用：集群所有资源最大化被使用
  * 效率：调度的性能要好，能尽快地对大批量的Pod完成调度工作
  * 灵活：允许用户根据自己的需求控制调度逻辑

* 调度过程

  * 过滤掉不满足条件的节点，这个过程称为：`predicate`
  * 对节点的优先级进行排序
  * 选择优先级最高的节点
  * **中间有一步有错误，直接返回错误**

  Predicate算法（常见的）：

  * PodFitsResources：节点上剩余的资源是否大于pod请求的资源

  * PodFitsHost：如果Pod指定了NodeName，检查节点名称是否和NodeName匹配

  * PodFitsHostPorts：节点上已经使用了的port是否和pod申请的port冲突

  * PodSelectorMatches：过滤掉和pod指定的label不匹配的节点

  * NoDiskConflict：已经mount的volume 和 pod指定的volume不冲突，除非他们都是只读

    

  ​       **如果在predicate过程中没有合适的节点，pod会一直在pending状态，不断重试调度，直到有节点满足条件。经过这个步骤，如果有多个节点满足条件，就继续priorities过程：按照优先级大小对节点排序**

  ​       优先级由一些列键值对组成，键是该优先级项的名称，值时它的权重（该项的重要性）。优先级都包括。

  * LeastRequestedPriority：通过计算CPU和Memory的使用率来决定权重，使用率越低权重越高。优先级指标更倾向于资源使用比例更低的节点
  * BalancedResourceAllocation：节点上的CPU和Memory使用率越接近，权重越高。这个应该和上面的一起使用，不应该单独使用
  * ImageLocalityPriority：倾向于已经有要使用镜像的节点，镜像总大小值越大，权重越高

  自定义调度器

  * 通过spec.schedulername参数指定调度器的名字

# 调度亲和性（Affinity）

Pod与Node之间：Pod.spec.nodeAffinity

* preferredDuringSchedulingIgnoredDuringExecution：软策略

* requiredDuringSchedulingIgnoredDuringExecution：硬策略

  requiredDuringSchedulingIgnoredDuringExecution

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: affinity
    labels:
      name: node-affinity-pod
  spec:
    containers:
    - name: myapp
      image: nginx
    affinity:
      nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
            - key: kubernetes.io/hostname
                operator: NotIn
                values:
                - k8s-node02
  ```

  preferredDuringSchedulingIgnoredDuringExecution

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: affinity
    labels:
      name: node-affinity-pod
  spec:
    containers:
    - name: myapp
      image: nginx
    affinity:
      nodeAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
          - preference:
            weight: 1
            mathExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
              - k8s-node02
  ```

Pod于Pod之间：pod.spec.affinity.podAffinity/podAntiAffinity

* preferredDuringSchedulingIgnoredDuringExecution：软策略

* requiredDuringSchedulingIgnoredDuringExecution：硬策略

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: pod-3
    labels:
      app: pod-3
  spec:
    containers:
    - name: pod-3
      image: nginx
    affinity:
      podAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
                - key: app
                  operator: In
                  values:
                    - pod-1
            topologyKey: kubernetes.io/hostname
      podAntiAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            podAffinityTerm:
              labelSelector:
                matchExpressions:
                  - key: app
                    operator: In
                    values:
                      - pod-2
              topologyKey: kubernetes.io/hostname
  ```

  键值运算关系

  * In：label的值在某个列表中

  * NotIn：label的值不再某个列表中

  * Gt：label的值大于某个值

  * Lt：label的值小于某个值

  * Exists：某个label存在

  * DoesNotExist：某个label不存在

    *如果nodeSelectorTerms下面有多个选项话满足一个条件即可，如果mathExpressions有多个选项，必须同时满足这些条件*

  亲和性/反亲和性调度策略比较如下：

  | 调度策略        | 匹配标签 | 操作符                                  | 拓扑域支持 | 调度目标                       |
  | --------------- | -------- | --------------------------------------- | ---------- | ------------------------------ |
  | nodeAffinity    | 主机     | In、NotIn、Exists、DoesNotExist、Gt、Lt | 否         | 指定主机                       |
  | podAffinity     | Pod      | In、NotIn、Exists、DoesNotExist         | 是         | pod指定Pod同一拓扑域           |
  | podAnitAffinity | Pod      | In、NotIn、Exists、DoesNotExist         | 是         | Pod于指定的Pod不再同一个拓扑域 |

  

# 污点（Taint）

*Taint使节点排除一类特定的Pod，toleration相互配合，可以用来避免Pod被分配到不适合的节点上。每个节点节点上可以有一个或多个Taint，表示不能容忍这些taint的pod运行在节点。如果将toleration应用于Pod上，则表示这些pod可以（但不要求）被调度到具有匹配taint的节点上*

1. 污点(Taint)的组成

   *使用kubectl taint设置污点。被设置污点后和Pod存在相斥关系，拒绝Node调度执行，甚至将node已存在的Pod驱逐出去*

   ```bash
   key=value:effect
   ```

   每个污点由key、value 作为污点标签，其中value为空，effect描述污点作用。当前taint effect支持如下三个选项：

   * NoSchedule：表示k8s不会将Pod调度到具有污点的Node上
   * PreferNoSchedule：表示k8s将尽量避免Pod调度到具有污点的Node上
   * NoExecute：表示k8s不会将Pod调度到具有污点的Node上，同时会将已存在的Node驱逐出去

2. 污点的设置、查看、和去除

   ```bash
   # 设置污点
   kubectl taint nodes node1 key1=value1:NoSchedule
   
   # 节点说明，查找Taints字段
   kubectl describe pod pod-name
   
   # 去除污点
   kubectl taint nodes node1 key1:NoSchedule-
   ```

**容忍（tolerations）**

pod.spec.tolerations 设置

```yaml
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
  tolerationSeconds: 3600
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
- key: "key2"
  operator: "Exists"
  effect: "NoSchedule"
```

* 其中key、value、effect要与Node上设置的taint保持一致
* operator的值为Exists将会忽略value值
* tolerationSeconds 用于描述当Pod需要被驱逐时可以在Pod上继续保留运行时间

1. 当不指定key值时，表示容忍所有的污点key:

   ```yaml
   tolerations:
   - operator: "Exists"
   ```

2. 当不指定effect值时，表示容忍所有的污点作用：

   ```yaml
   tolerations:
   - key: "key"
   	operator: "Exists"
   ```

3. 有多个master存在时，防止资源浪费，可以如下设置：

   ```yaml
   kubectl taint nodes NodeName node-role.kubernetes.io/master=:PreferNoSchedule
   ```

   

# 固定节点

1. `pod.spec.nodeName`将Pod直接调度到指定的Node节点上，会跳过Schedule的调度策略，该匹配规则是强制匹配

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: myweb
   spec:
     selector:
       matchLabels:
         app: myweb
     template:
       metadata:
         labels:
           app: myweb
       spec:
         nodeName: k8s-node01
         containers:
         - name: myweb
           image: <Image>
           ports:
           - containerPort: <Port>
   ```

2. `pod.spec.nodeSelector`通过k8s的 label-selector机制选择节点，由调度器调度策略匹配label，而调度Pod到目标节点，该匹配规则属于强制约束

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: myweb
   spec:
     selector:
       matchLabels:
         app: myweb
     template:
       metadata:
         labels:
           app: myweb
       spec:
         nodeSelector:
           nginx01: sshfast
         containers:
         - name: myweb
           image: <Image>
           ports:
           - containerPort: <Port>
   ```

   