# 控制器类型

> * ReplicationController和ReplicaSet
>
> * Deployment
>
>   ![image-20200607172835277](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200607172835277.png?image/auto-orient,1/quality,q_90)

> 
>   * 属于申明式控制器，使用**apply**创建
>   * 创建流程
>     * Deploy 创建rs（replicaSet）
>     * rs创建Pod
>   * 扩容
>     * *手动扩容*```kubectl scale deployment nginx --replicas=10```
>     * *hpa自动扩容(horizontal pod autoscaling)*```kubectl autoscale deployment nginx --min=10 --max=15 --cpu-percent=80 ```
>   * 更新镜像
>     * ```kubectl set image deployment/nginx nginx=registry.cn-shanghai.aliyuncs.com/callum_namespace/myapp:v1	```
>     * 策略：可以保证在升级时有一定数量的Pod是down的。默认的他会确保至少有比期望的Pod数量少一个是up状态（最多一个不可用）*新的版本中从1-1变成25%-25%*
>   * 回滚
>     * *回滚上一个版本*```kubectl rollout undo deployment/nginx```
>     * *回滚指定的版本*```kubectl rollout undo deployment/nginx --to-reverison=2``` 
>     * 暂停滚动更新```kubectl rollout pause deployment/nginx```
>     * *查看回滚时的状态*```kubectl rollout status deployment nginx```
>     * *查看历史状态*```kubectl rollout history deployment/nginx```
>     * 策略：多个rollout并行，假设有5个nginx:1.7.9 replica Deployment，创建到3个时又开始更行含有5个nginx:1.9.1 replica Deployment，此时Deployment 会立即删除，并创建新的nginx:1.9.1Pod，不会等前一个创建完成才更新。
>   * 清理Policy
>     * 可以通过设置```.spec.reversionHistoryLimit```项来指定Deployment保留reversion的历史记录，默认会保留所有的reversion,如果为0 Deployment就不允许回滚了
>
> * DaementSet
>
> * StatefullSet
>
>   * 创建statefullSet 需要创建一个无头服务（handless service[^clusterIP:None]）
>   * 结合存储PVC实验来看
>     * 匹配Pod name（网络标识）的模式为：$(statefullSet名称)-$(序号)，比如上面的实例：web-0,web-1,web-2
>     * StatefullSet为每个Pod副本创建了一个Dns域名，这个域名的格式为：$(podname).(handless server name),也就是意味着服务间是通过Pod域名来通信而非Pod IP，因为当Pod所在的Node发生故障时，Pod会被漂移到Node上，Pod IP会发生变化，但是Pod域名不会有变化
>     * StatefullSet使用了Handless服务来控制Pod的域名，这个域名的FQDN为：(servicename).(namespace).svc.cluster.local  其中，"cluster.local"指的是集群的域名
>     * 根据volumeClaimTemplates，为每个Pod创建一个pvc，pvc的命名规则匹配模式：(volumeClaimTemplates.name)-(pod_name)，比如上面的volumeMounts.name=www,Podname=web[0-2]，因此创建出来的PVC是www-web-0、www-web-1、www-web-2
>     * 删除Pod不会删除其PVC，手动删除pvc将自动释放PV
>
> * Job/CronJob
>
>   * Cronjob 
>
>     * 在给定的时间运行一次
>
>     * 周期性的在给定的时间点运行
>
>     * spec.template 格式同Pod
>
>     * restartPolicy仅支持Never和OnFailure
>
>     * 单个Pod时，默认Pod成功运行后Job即结束
>
>     * `.spec.completions`标志job结束需要成功运行的Pod个数，默认为1
>
>     * `.spec.parallelism`标志并行运行的Pod的个数，默认为1
>
>     * `.spec.activeDeadlineSeconds`标志失败Pod的重试最大时间，超过这个时间不会继续重试
>
>     * `.spec.schedule`调度，必须字段，指定任务运行周期，格式同Cron
>
>     * `.spec.jobTemplate`Job模板，必须字段，指定需要运行的任务，格式同Job
>
>     * `.spec.startingDeadlineSeconds`启动Job的期限（秒级别），该字段是可选的。如果因为任何原因而错过了被调度的时间，那么错过执行时间的Job将被认为是失败的。如果没有指定，则没有期限。
>
>     * `.spec.concurrencyPolicy`并发策略（可选），他指定了如果处理被CronJob创建的Job的并发指定。只允许指定下面策略的一种：
>
>       * allow（默认）：允许兵法运行Job
>
>       * Forbid：禁止并发运行，如果前一个还没有完成，则直接跳过下一个
>
>       * Replace :取消当前正在运行的Job，用一个新的来替换
>
>         *注意：当前策略只能应用于同一个CronJob创建的Job，如果存在多个CronJob，他们创建的Job之间总是允许并发执行*
>
>     * `.spec.suspend`:挂起，该字段也是可选的。如果设置为True，后续所有执行都会被挂起，他对已经开始的Job不起作用。默认值为false
>
>     * `.spec.successfullJobHistoryLimit`和`.spec.failesJobsHistoryLimit`：历史限制（可选）。他们制定了可以保留多少完成和失败的Job。默认情况下，他们分别设置为 3 和 1.设置限制的值为0，相关类型的Job完成后将不会被保留。
>
>     * 典型用法
>
>       * 在给定的时间点调度Job运行
>       * 创建周期性的运行Job，例如：数据库备份、发送邮件
>
> * Horizontal Pod Aotuscaling
>
>   * 作为一个附属品方式来管理其他控制器
>   * 根据数据类型的方式扩容缩