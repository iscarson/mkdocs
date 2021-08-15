
* 介绍说明
  * k8s组件说明
    * borg组件说明
    * k8s结构说明
      * 网络结构
      * 组件结构
  * K8s 中的一些关键字解释

* 基础概念
  * Pod 概念
    * 自主式Pod
    * 管理器管理的Pod
      * Rs、Rc
      * deployment
      * Hpa
      * statefullset
      * Daemonset
      * Job,Cronjob
    * 服务发现
    * Pod协同
  * 网络通讯模式
    * 网络通讯模式说明
    * 组件通讯模式说明
* Kubernetes 安装
  * 系统初始化
  * Kubeadm 部署安装
  * 常见问题分析
* 资源清单
  * K8s 中资源的概念
    * 什么是资源
    * 名称空间级别的资源
    * 集群级别的资源
  * 资源清单-yaml语法格式
  * 通过资源清单编写Pod
  * 通过资源清单编写Pod
  * Pod的生命周期
    * initC
    * Pod phase
    * 容器探针
      * livenessProbe
      * readinessProbe
    * Pod hook
    * 重启策略

* Pod控制器
  * Pod控制器说明
    * 什么是控制器
    * 控制器类型说明
      * replicationController 和 RelicaSet
      * Deployment
      * DaemonSet
      * Job 
      * Cronjob 
      * Statefullset 
      * Horizontal Pod Autoscaling
* 服务发现
  * Service 原理
    * Service 含义
    * Service 常见类型
      * ClusterIP
      * NodePort
      * ExternalName
    * Service 实现方式
      * Userspace 
      * Iptables 
      * Ipvs 
    * Ingress 
      * Nginx 
        * http代理访问
        * https代理访问
        * 使用cookie 实现会话关联
        * BasicAuth
        * Nginx 进行重写

* 存储
  * PV
    * 概念解释
      * Pv
      * Pvc
      * 类型说明
    * Pv
      * 后端类型
      * Pv 访问模式说明
      * 回收策略
      * 状态
      * 实例演示
    * Pvc
      * Pvc实战演示
  * volume
    * 定义概念
      * 卷的类型
    * EmptyDir
      * 说明
      * 用途假设
      * 实验演示
    * hostPath
      * 说明
      * 用途说明
      * 实验演示
  * Secret 
    * 定义概念
      * 概念说明
      * 分类
    * Service Account
    * Opaque Secret
      * 特殊说明
      * 创建
      * 使用
        * Secret 挂载到 Volume
        * Secret 导出到环境变量中
    * Kubernetes.io/dockerconfigjson
  * configMap
    * 定义概念
    * 创建 configMap
      * 使用目录创建
      * 使用文件创建
      * 使用字面值创建
    * Pod 中使用configMap
      * configMap 来代替环境变量
      * configMap 设置命令行参数
      * 通过数据卷插件使用configMap
    * configMap 热更新
      * 实现演示
      * 更新触发说明
* 调度器
  * 调度器概念
    * 概念
    * 调度过程
    * 自定义调度器
  * 调度亲和性
    * nodeAffinity
      * preferredDuringSchedulinglgnoredDuringExecution
      * requiredDuringSchedulinglgnoredDuringExecution
    * podAntiAffinity
      * preferredDuringSchedulinglgnoredDuringExecution
      * requiredDuringSchedulinglgnoredDuringExecution
    * 亲和性运算符
  * 污点
    * 污点概念
    * Taint 
      * 组成
      * 污点的设置、查看和去除
    * Tolerations 
      * Tolerations设置演示
  * 固定节点调度
    * PodName 指定调度
    * 标签选择器调度
* 集群安全机制
  * 准人控制
  * 鉴权
  * 认证
    * http token
    * http base
    * Https
  * 机制说明
    * AlwaysDeny
    * AlwaysAllow
    * ABAC
    * Webhook
    * RBAC
      * RBAC
      * Role and ClusterRole
      * RouleBinding and ClusterRoleBinding
      * Resources
      * To Subjects
      * 创建一个系统用户管理k8s dev 名称空间:重要试验
* HELM
  * HELM概念
    * HELM概念说明
    * 组件构成
    * HELM部署
    * HELM自定义
  * HELM部署实例
    * HELM部署dashboard
    * Metrics-server
      * HPA演示
      * 资源限制
        * Pod
        * 名称空间
    * Prometheus 
    * EFK
* 运维
  * Kubeadm 源码修改
  * Kubernetes 高可用构建

