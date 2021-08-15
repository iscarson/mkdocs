```bash
mkdir /nfs{1..3} # 创建 nfs1 nfs2 nfs3

docker tag oldimage:v1  newimage:v1

kubectl run nginx --image=nginx

kubectl explain pod 
kubectl apply -f xxx.yml	# 运行yaml 文件创建pod，create也可以创建
kubectl logs myapp-pod -c test # -c 代表pod中的容器
kubectl describe pod myapp-pod # describe 可以查看pod中的信息
kubectl get pod -o wide --show-labels # -o wide 可以显示详细信息 show-labels可以显示rs定义的标签
kubectl edit pod myapp-pod # 编辑正在运行的容器
kubectl exec readiness-httpget-pod -it -- /bin/sh # 进入到pod容器，多个容器使用-c参数指定
kubectl delete pod --all # 默认删除default 命名空间下的所有容器

kubectl label pod node01 app=node02 --overwrite=true # 添加标签
kubectl label pod frontend-2ndz7 tier=frontend1 --overwrite=true # 更改已存在的rs标签

kubectl apply -f deployment.yml --record # record 可以记录回滚的历史过程
kubectl scale deployment nginx --replicas=10 #  scale 扩容deploy pod为10个
kubectl set image deployment/nginx nginx=nginx:1.9.1 # 更新deployment 镜像
kubectl autoscale deployment nginx --min=10 --max=15 --cpu-percent=80 # 利用hpa设定阈值自动更新
kubectl rollout undo deployment/nginx # 回滚 deployment 
kubectl rollout undo deployment/nginx --to-reverison=2 # 回滚到指定的版本
kubectl rollout pause deployment/nginx # 暂停滚动更新
kubectl rollout status deployment/nginx # 查看回滚状态
kubectl rollout history deployment/nginx # 查看历史状态

# 创建存储configmap 目录
kubectl get cm
kubectl describe cm game-config 
kubectl get cm game-config -o yaml
kubectl create configmap game-config --from-file=docs/user-guide/configmap/kubectl
```

**metadata 标签	和selector标签要对应**

> * 端口暴露方式 NodePort   ingress 

> Minikube 本地docker 环境启动
>
> ```minikube start --vm=true --base-image=registry.cn-hangzhou.aliyuncs.com/google_containers/kicbase:v0.0.10```
>
> https://ribhvzyz.mirror.aliyuncs.com
>
> --vm-driver=docker 
>
> --image-repository=
>
> --image-mirror-country=cn
>
> --registry-mirror=["https://ribhvzyz.mirror.aliyuncs.com"]
>
> * 默认启动的方式加速
>
> minikube start  --registry-mirror https://ribhvzyz.mirror.aliyuncs.com --image-mirror-country=cn

|                          |          |
| ------------------------ | -------- |
| 资源类型                 | 缩写别名 |
| clusters                 |          |
| componentstatuses        | cs       |
| configmaps               | cm       |
| daemonsets               | ds       |
| deployments              | deploy   |
| endpoints                | ep       |
| event                    | ev       |
| horizontalpodautoscalers | hpa      |
| ingresses                | ing      |
| jobs                     |          |
| limitranges              | limits   |
| namespaces               | ns       |
| networkpolicies          |          |
| nodes                    | no       |
| statefulsets             |          |
| persistentvolumeclaims   | pvc      |
| persistentvolumes        | pv       |
| pods                     | po       |
| podsecuritypolicies      | psp      |
| podtemplates             |          |
| replicasets              | rs       |
| replicationcontrollers   | rc       |
| resourcequotas           | quota    |
| cronjob                  |          |
| secrets                  |          |
| serviceaccount           | sa       |
| services                 | svc      |
| storageclasses           |          |
| thirdpartyresources      |          |