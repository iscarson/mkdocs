# 机制说明

*围绕APIServer来设计，使用了认证（Authentication）、鉴权（Authorization）、准入控制（AdmissionControl）三部来保证APIServer的安全*

![image-20200609215752076](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200609215752076.png?image/auto-orient,1/quality,q_90)


# 认证

* Http Token认证：生成一个复杂的字符串，每一个Token对应一个用户名存储在Api Server能访问的文件中。当客户端发起API调用请求时，需要在HTTP Hander中放入Token
* Http Base认证：用户名+密码，使用BASE64编码将字符串放在HTTP Request中的Heather Authorization域里发送到服务端，服务端收到后进行解码，获取用户名和密码
* 最严格的Https证书认证：基于CA根证书签名的客户端身份认证方式

**HTTPS证书认证**

![image-20200609215844364](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200609215844364.png?image/auto-orient,1/quality,q_90)


**需要认证的节点**

![image-20200609220125572](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200609220125572.png?image/auto-orient,1/quality,q_90)


**两种类型**

* Kubernetes组件对API Server的访问：kubectl、Controller Manager、Scheduler、kubelet、kube-proxy
* Kubernetes管理的Pod对容器的访问：Pod（dashboard也是以Pod形式运行）

**安全性说明**

* Controller Manager、Scheduler与API Server在同一台机器，所以直接使用API Server的非安全端口访问，`--insecure-bind-address=127.0.0.1`
* Kubectl、Kubelet、kube-proxy访问API Server就都需要证书进行HTTPS双向认证

**证书颁发**

* 手动签发：通过k8s集群的根ca进行签发HTTPS证书
* 自动签发：kubelet首次访问API Server时，使用token做认证，通过后Controller Manager会为kubelet生成一个证书，以后的访问都是用证书做认证了。

**kubeconfig**

kubeconfig 文件包含集群参数（CA证书、API Server地址），客户端参数（上面生成的证书和私钥），集群context信息（集群名称、用户名）。Kubenetes组件通过启动时指定不同的kubeconfig文件可以切换到不同的集群。

**ServiceAccount**

Pod中的容器访问API Server。因为Pod的创建、销毁时动态的，所以要为他手动生成证书就不行了，kubernetes使用了Service Account解决了Pod访问API Server的认证问题。

**Secret与SA的关系**

Kubernetes设计了一种资源对象叫做Secret，分为两类一种是ServiceAccount的service-account-token，另一种是用于保存用户自定义保密信息的Opaque。ServiceAccount中用到包含三个部分：Token、ca.crt、namespace

* token是使用API Server私钥签名的JWT。用于访问API Server时，Server端认证

* ca.crt，根证书。用于Client端验证API Server发送端证书

* namespace，表示这个service-account-token的作用域名空间

  *默认情况下，每个namespace都会有一个ServiceAccount，如果Pod在创建时没有指定ServiceAccount，就会使用Pod所属的namespace的ServiceAccount*

  `默认挂载目录：/run/secrets/kubernetes.io/serviceaccount/`

  ```shell
  kubectl get secret --all-namespaces
  kubectl describe secret default-token-5gm94 --namespace=kube-system
  ```

![image-20200609224859184](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200609224859184.png?image/auto-orient,1/quality,q_90)




# 鉴权

*认证只是说明不同组件间是否可以通讯，而鉴权是确定请求方有哪些访问资源的权限。APIServer目前支持以下几种授权策略（通过APIServer的启动参数"--authorization-mode"设置）*

* AlwaysDeny：表示拒绝所有的请求，一般用于测试
* AlwaysAllow：允许接收所有请求，如果集群不需要授权流程，则可以采用该策略
* ABAC（Attribute-Based Access Control）:基于属性的访问控制，表示使用用户配置的授权规则对用户请求进行匹配和控制
* Webhook：通过调用外部Rest服务对用户进行授权
* RBAC（Role-Based Access Control）：基于角色的访问控制，现行默认规则

**RBAC授权模式**

*RBAC（Role-Based Access Control）基于角色的访问控制，在Kubernetes1.5中引入，现在称为默认标准。相对其他访问控制方式，拥有以下优势*

* 对集群中的资源和非资源均拥有完整的覆盖
* 整个RBAC完全由几个API对象完成，同其他API对象一样，可以用kubectl或API进行操作
* 可以在运行时进行调整，无需重启API Server

1. RBAC的API资源对象说明

   *RBAC引入4个心的顶级资源对象：role、clusterRole、clusterRoleBinding，四种对象类型均可以通过kubectl与API操作*

![image-20200609230514028](http://carson-mweb.oss-cn-beijing.aliyuncs.com/2020/06/14/image20200609230514028.png?image/auto-orient,1/quality,q_90)


   **注意⚠️：kubernetes并不会提供用户管理，那么user、group、serviceAccount指定的用户在kubernetes组件（kubectl、kube-proxy）或其他自定义的用户再向CA申请证书时，需要提供一个证书请求文件**

   ```json
   {
     "CN": "admin"
     "host": [],
   	"key": {
       "algo": "rsa",
       "size": 2048
     },
   	"name": [
       {
         "C": "CN"
         "ST": "HangZhou"
         "L": "XS",
         "O": "system:masters",
         "OU": "System"
       }
     ]
   }
   ```

* APIServer会把客户端证书的CN字段作为User，把name.0字段作为Group
* kubelet使用TLS Bootstaping认证时，APIServer可以使用Bootstrap Tokens或者Token authentication file验证 =token，无论哪一种，kubernetes都会为token绑定一个默认的User和Group
* Pod使用serviceAccount认证时，service-account-token中的JWT会保存User信息

Role and ClusterRole

*在RBAC API中，Role表示一组规则权限，权限只会增加（累加权限），不存在一个资源已开始就有很多权限而通过RBAC对其进行减少的操作；Role可以定义在一个namespace中，如果想要夸namespace则可以创建ClusterRole*

```yaml
kind: Role
apiVerion: rbac.authorization.k8s.io/v1beta1
metadata:
	namespace: default
	name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core api group 赋予API版本的哪个组
	resources: ["pods"]
	verbs: ["get","watch","list"]
```

*ClusterRole具有Role相同的权限角色控制能力，不同的是ClusterRole是集群级别的，ClusterRole可以用于*

* 集群级别的资源控制（例如Node访问权限）

* 非资源型endpoint（例如/healthz访问）

* 所有命名空间资源控制（例如pods）

  ```yaml
  kind: ClusterRole
  apiVersion: rbac.authorization.k8s.io/v1beta1
  metadata:
    name: secret-reader
  rules:
  - apiGroups: [""]
    resources: ["secrets"]
    verbs: ["get","watch","list"]
  ```

**RoleBinding and ClusterBinding**

*RoleBinding 可以将角色中定义的权限授予用户或用户组，RoleBinding包含一组权限列表（subjects），权限里表中包含有不同形式的待授予权限资源类型（user、groups、service accounts）；RoleBinding同样包含对被Bind的Role引用；RoleBinding适用于某个命名空间内授权，而ClusterRoleBinding适用于集群范围内的授权*

***将default命名空间的`pod-reader`用户授权与jane用户，此后jane用户在default命名空间中具有`pod-reader`的权限***

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

*RoleBinding同样可以引用ClusterRole对当前namespace内用户、用户组或ServiceAccount进行授权，这种操作允许集群管理员在整个集群内定义一些通用的ClusterRole，然后在不同的namespace中使用RoleBinding来引用**

*例如，以下RoleBinding引用了一个ClusterRole，这个ClusterRole具有整个集群内对secrets的访问权限；但是其授权用户dave只能访问development空间中的secrets（因为RoleBinding定义在development命名空间）*

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: read-secrets
  namespace: development
subjects:
- kind: User
  name: dave
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

*使用ClusterRoleBinding可以对整个集群中所有命名空间资源权限进行授权；以下ClusterRoleBinding样例展示了授权manager组内所有用户在全部命名空间中对secrets进行访问*

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

**Resources**

*kubernetes集群内一些资源一般以其名称字符串来表示，这些字符串一般会在API的Url地址中出现；同时某些资源也会包含子资源，例如logs资源就属于pods的子资源，API中的url样例如下*

`GET /api/v1/namespaces/{namespace}/pods/{name}/log`

*如果要在RBAC授权模型中控制这些子资源的访问权限，可以通过/分隔符来实现，以下是一个定义pods资源logs访问权限的Role定义样例*

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: default
  name: pod-and-pod-logs-reader
rules:
- apiGroups: [""]
  resources: ["pods","pods/log"]
  verbs: ["get","list"]
```

**to Subjects**

*RoleBinding和ClusterRoleBinding可以将Role绑定到Subjects；Subjects可以是groups、users或者是service accounts*

*Subjects中Users使用字符串表示，它可以是一个普通的名字字符串，如"alice";也可以是email格式的邮箱地址，如"[liangml0528@163.com](liangml0528@163.com)";甚至是一组字符串形式的数字ID。但是Users的前缀system：是系统保留的，集群管理员可以确保普通用户不会使用这个前缀的格式*

*Groups书写格式与Users相同，都为一个字符串，并且没有特定的格式要求；同样system：前缀为系统保留*

***创建一个用户只能管理dev空间***

```shell
{
  "CN": "devuser",
  "hosts": [],
  "key": {
    "algo": "rsa",
    "size": 2048
  },
  "name": [
    {
      "C": "CN",
      "ST": "BeiJing",
      "L": "Beijing",
      "O": "k8s",
      "OU": "System"
    }
  ]
}
# 下载证书生成工具
wget https://pkg.cfssl.org/R1.2/cfssl_linux-amd64
mv cfssl_linux-amd64 /usr/local/bin/cfssl

wget https://https://pkg.cfssl.org/R1.2/cfssljson_linux-amd64
mv cfssljson_linux-amd64 /usr/local/bin/cfssljson

wget https://https://pkg.cfssl.org/R1.2/cfssl-certinfo_linux-amd64
mv cfssl-certinfo_linux-amd64 /usr/local/bin/cfssl-certinfo

cfssl gencert -ca=ca.crt -ca-key=ca.key -profile=kubernetes /root/devuser-csr.json | cfssljson -bare devuser

# 设置集群参数
export KUBE_APISERVER="https://172.20.0.113:6443"
kubectl config set-cluster kubernetes \
--certificate-authority=/etc/kubernetes/ssl/ca.crt \
--embed-certs=true \
--server=$(KUBE_APISERVER) \
--kubeconfig=devuser.kubeconfig

# 设置客户端认证参数
kubectl config set-credentials devuser \
--client-certificate=/etc/kubernetes/ssl/devuser.pem \
--client-key=/etc/kubernetes/ssl/devuser-key.pem \
--embed-certs=true \
--kubeconfig=devuser.kubeconfig
 
 # 设置上下文参数
 kubectl config set-context kubernetes \
 --cluster=kubernetes \
 --user=devuser \
 --namespace= dev \
 --kubeconfig=devuser.kubeconfig
 
 # 设置默认上下文
 kubectl config use-context kubernetes --kubeconfig=devuser.kubeconfig
 
 cp -f ./devuser.kubeconfig /root.kube/config
 
 kubectl create rolebinding devuser-admin-binding --clusterrole=admin --user=devuser --namespace=dev
```



# 准入控制

*准入控制是APIServer的插件集合，通过添加不同的插件，实现额外的准入控制规则，甚至基于APIServer的一些主要功能都需要通过Admission Controllers实现，比如ServiceAccount*

**针对不同版本的准入控制推荐列表：1.14推荐**

```bash
NamespaceLifecycle、LimitRanger、ServiceAccount、DefaultStorageClass、DefaultTolerationSeconds、MutatingAdmissionWebhook、ValidatingAdmssionWebhook、ResourceQupta
```

**列举几个插件的工呢个**

* NamespaceLifecycle：防止在不存在的namespace上创建对象，防止删除系统预置namespace，删除namespace时，连带删除它的所有资源对象。
* LimitRanger：确保请求的资源不会超过资源所在的Namespace和LimitRange的限制
* ServiceAccount：实现了自动化添加ServiceAccount
* ResourceQupta：确保请求的资源不会超过资源的ResourceQupta限制