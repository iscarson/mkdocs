**helm有两个重要的概念：chart和release**

* chart 是创建一个应用的信息集合，包括各种kubernetes对象的配置模版、参数定义、依赖关系、文档说明等。chart时应用部署等自包含逻辑单元。可以将chart想象成apt、yum中的软件包
* release是chart的运行实例，代表了一个正在运行的应用。当chart被安装到kubernetes集群，就生成了一个release。chart能够多次安装到同一个集群，每次安装都是一个release

![image-20200610110737820](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200610110737820.png?image/auto-orient,1/quality,q_90)



*helm客户端负责chart和release的创建和管理以及和Tiller的交互。Tiller服务器运行在kubernetes集群中，它会处理helm客户端的请求，与kubernetes API Server交互*

**部署helm**

```bash
wget  https://get.helm.sh/helm-v3.2.3-darwin-amd64.tar.gz

cp helm /usr/local/bin/
```

**kubernetes API Server 开启了RBAC访问控制，所以需要创建tiller使用的service account:tiller并分配合适的角色给它，这里直接分配cluster-admin 这个集群内置的clusterRole给它。helm文档[Role-based Access Control](https://docs.helm.sh/using_helm/#role-based-access-control)**

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

```bash
kubectl create -f rbac-config.yaml
----
serviceaccount/tiller created
clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

```bash
helm  init  --service-account tiller --skip-refresh
```

**Helm自定义模板**

```bash
# 创建文件夹
mkdir ./hello-world
cd ./hello-world

# 创建自描述文件chart.yaml，这个文件必须有name 和 version定义
cat <<"EOF">> ./chart.yaml
name: hello-world
version: 1.0.0
EOF

#创建模版文件，用于生成kubernetes资源清单（mainfests）
mkdir ./templates
cat <<'EOF'>> ./templates/deployment.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: hello-world
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
        - name: hello-world
          image: nginx
          ports:
            - containerPort: 80
              protocol: TCP
EOF
cat <<'EOF'>> ./templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-world
spec:
  type: NodePort
  selector:
    app: hello-world
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
EOF

# 使用命令： helm install RELATIVE_PATH_TO_CHART 创建一此Release
helm install .
```

**动态配置**

```bash
# 配置体现在配置文件values.yaml
cat <<'EOF'>> ./values.yaml
image:
	repository: gcr.io/google-samples/node-hello
	tag: '1.0'
EOF

# 这个文件中定义的值，在模版文件中可以通过.values对象访问到
cat <<'EOF'>> ./templates/deployment.yaml
kind: Deployment
metadata:
	name: hello-world
spec:
	replicas: 1
	template:
		metadata:
			labels:
				app: hello-world
    spec:
    	containers:
    		- name: hello-world
    			image: {{ .Values.image.repository}}:{{.Values.image.tag}}
    			ports:
    				- containerPort: 8080
    					protocol: TCP
EOF

# 在values.yaml中的值可以被部署到release 时用到的参数 --values YAML_FILE_PATH 或 --set key1=values1 key2=values2 覆盖掉
helm install --set image.tag='latest' .

# 升级版本
helm upgrade -f values.yaml test .
```

**helm 命令**

```bash
# 列出已经部署的Release
helm ls
# 查询一个特定的Release的状态
helm status RELEASE_NAME
# 移除所有与这个Release相关的Kubernetes资源
helm delete cautious-shrimp
helm rollback RELEASE_NAME REVISION_NUMBER
helm rollback cautious-shrimap 1
# 使用helm delete --purge RELEASE_NAME 移除所有与指定Release相关的Kubernetes资源和所有这个Release的记录
helm delete --purge cautious-shrimp
helm ls --deleted
```

**Debug**

```bash
# 使用模版动态生成k8s资源清单，非常需要能提前预览生成的效果
# 使用--dry-run -debug 选项来打印生成的清单文件系统，而不是部署
helm install . --dry-run --debug --set images.tag=latest
```

# helm 部署dashborad

*kubernetes-dashborad.yaml*

```yaml
image:
  repository: k8s.gcr.io/kubernetes-dashboard-amd64
  tag: v1.10.1
ingress:
  enabled: true
  hosts:
    - k8s.frognew.com
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
  tls:
    - secretName: frognew-com-tls-secret
      hosts:
      - k8s.frognew.com
rbac:
  clusterAdminRole: true
```

```bash
helm install stable/kubernetes-dashboard \
-n kubernetes-dashboard \
--namespace kube-system \
-f kubernetes-dashboard.yaml
```

```shell
kubectl -n kube-system get secret |grep kubernetes-dashboard-token # 查看dashboard token
```

**helm Prometheus部署**

P61-10-4