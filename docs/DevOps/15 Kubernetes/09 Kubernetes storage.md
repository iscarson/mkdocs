* 分类

  * configMap（起到存储配置文件的功能）

    * 使用目录创建

      `kubectl create configmap game-config --from-file=docs/user-guide/configmap/kubectl`

      *--from-file 指定在目录下的所有文件都会被用在configmap里面创建一个键值对，键的名字就是文件名，值就是文件的内容*

      **目录中有两个文件**

      ```bash
      $ cat docs/game.properties
      
      enemies=aliens
      lives=3
      enemies.cheat=true
      enemies.cheat.level=noGoodRotten
      secret.code.passphrase=UUDDLRLRBABAS
      secret.code.allowed=true
      secret.code.lives=30
      
      $ cat docs/ui.properties
      
      color.good=purple
      color.bad=yellow
      allow.textmod=true
      how.nice.to.look=fairlyNice
      
      kubectl get cm
      kubectl describe cm game-config
      kubectl get cm game-config -o yaml
      kubectl create configmap game-config --from-file=../dir/ # 创建configmap
      ```

      

    * 使用文件创建

      `kubectl create configmap game-config --from-file=../game.properties`**与目录创建并无什么区别只是将目录换成文件**

    * 使用字面值创建

      * `--from-literal`**使用字面值的方式创建该参数可以使用多次**,

        `kubectl create configmap special-config --from-literal=specal.how=very --from-literal=special.type=charm`

        `kubectl get cm special-config -o yaml`

    * **Pod中ConfigMap使用**

      * 使用ConfigMap来代替环境变量

        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: special-config
          namespace: default
        data:
          special.how: very
          special.type: charm
        ```

        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: env-config
          namespace: default
        data:
          log_level: INFO
        ```
        
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: dapi-test-pod
        spec:
          containers:
          - name: test-container
            image: nginx
            command:
              - /bin/sh
              - -c
              - env
            env:  # 指定导入方式
              - name: SPECIAL_LEVEL_KEY
                valueFrom:
                    configMapkeyRef:
                      name: special-config
                      key: special.how
              - name: SPECIAL_LEVEL_KEY
                valueFrom:
                    configMapkeyRef:
                      name: special-config
                      key: special.type
            envFrom:  # 全部导入方式
              - configMapRef:
                  name: env-config
          restartPolicy: Never
        ```
        
      * ConfigMap设置命令行参数

        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: dapi-test-pod
        spec:
          containers:
            - name: test-container
              image: nginx
              command: # command方式导入
                - /bin/sh
                - -c
                - echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)
              env:  # 指定导入方式
                - name: SPECIAL_LEVEL_KEY
                  valueFrom:
                      configMapkeyRef:
                        name: special-config
                        key: special.how
                - name: SPECIAL_LEVEL_KEY
                  valueFrom:
                      configMapkeyRef:
                        name: special-config
                        key: special.type
            restartPolicy: Never
        ```

      * 通过数据卷插件使用

        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: dapi-test-pod
        spec:
          containers:
            - name: test-container
              image: nginx
              command:
                - /bin/sh
                - -c
                - cat /etc/config/special.how
              volumeMounts:
                - name: config-volume
                  mountPath: /etc/config
          volumes:
            - name: config-volume
              configMap:
                  name: special-config
          restartPolicy: Never
        ```

      * ConfigMap热更新

        ```yaml
        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: log-config
          namespace: default
        data:
          log_level: INFO
        ---
        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata:
          name: my-nginx
        spec:
          replicas: 1
          template:
            metadata:
              labels:
                run: my-nginx
            spec:
              containers:
                - name: my-nginx
                  image: nginx
                  ports:
                    - containerPort: 80
                  volumeMounts:
                  - name: config-volume
                    mountPath: /etc/config
              volumes:
                - name: config-volume
                  configMap:
                      name: log-config
        ```

        * 修改ConfigMap，等待大约10s钟时间

          > `kubectl edit configmap log-config`

        * 如果想达到热更新的效果可以通过修改`pod annottations`的方式强制触发滚动更新

          * 这个例子里我们在`.spec.template.metadata.annotations`中添加version/config,每次通过修改version/config来触发滚动更新

          > `kubctl patch deployment my-nginx --patch '{"spec":{"template":{"metadata":{"annotations":{"version/config":"20190411"}}}}}'`

  * Secret（加密的信息）

    * Service Account：用来访问Kubernetes API，由k8s自动创建，并且挂载到Pod的`/run/secrets/kubernetes.io/serviceaccount`目录中（**Pod访问apiServer方式**）

    * Opaque：base64编码格式的Secret，用来存储密码、密钥等

      * map类型，要求value 是base64编码格式

        ```bash
        ➜  ~ echo -n "admin"|base64
        YWRtaW4=
        ➜  ~ echo -n "YWRtaW4=" |base64
        WVdSdGFXND0=
        ➜  ~ echo -n "YWRtaW4=" |base64 -d
        admin
        ```

        ```yaml
        apiVersion: v1
        kind: Secert
        metadata:
        	name: mysecert
        type: Opaque
        data:
        	password: WVdSdGFXND0=
        	username: YWRtaW4=
        ```

      * 使用方式

        * 挂载到volume

          ```yaml
          apiVersion: v1
          kind: Pod
          metadata:
            name: secret-test
            labels:
              name: secret-test
          spec:
            volumes:
              - name: secrets
                secret:
                    secretName: mysecret
            containers:
            - name: db
              image: nginx
              volumeMounts:
              - name: secrets
                mountPath: "/etc/secrets"
                readOnly: true
          ```

        * 导入到环境变量

          ```yaml
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: pod-deployment
          spec:
            replicas: 2
            selector:
              matchLabels:
                app: pod-deployment
            template:
              metadata:
                labels:
                  app: pod-deployment
              spec:
                containers:
                - name: pod-1
                  image: nginx
                  ports:
                  - containerPort: 80
                  env:
                    - name: TEST_USER
                      valueFrom:
                        secretKeyRef:
                          key: username
                          name: mysecret
                    - name: TEST_PASSWORD
                      valueFrom:
                        secretKeyRef:
                          key: password
                          name: mysecret
          ```

    * Kubernetes.io/dockerconfigjson：用来存储私有docker registry的认证信息

      * 使用kubectl创建docker registry 认证的secret

        `kubectl create secret docker-registry myregistrykey --docker-server=DOCKER_REGISTRY_SERVER --docker-username=DOCKER_USER --docker-password=DOCKER_PASSWORD --docker-email=DOCKER_EMAIL`

      * 创建Pod的时候通过 imagePullSecrets来引用刚创建的`myregistrykey`

        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
        	name: foo
        spec:
        	containers:
        		- name: foo
        			image: nginx
          imagesPullSecrets:
          	- name: myregistrykey
        ```

  * volume（Pod提供存储卷）

    * k8s支持存储卷的类型

      * awsElasticBlockStore、azureDisk、azureFile、cephfs、csi、downwardAPIemtyDir
      
      * fs、flocker、gcepersistentDisk、gitRepo、glusterfs、hostPath、iscsi、local、nfs
      * persistentVolumeClaim、projected、portworxVolume、quobyte、rbd、scaleIO、secret
      * storageos、vsphereVolume
      
    * emptyDir

      * Pod启动时会创建一个空的目录，具有读写的权限。任何原因从节点中删除Pod时，emptyDir中的数据被永久删除（崩溃不会导致数据丢失）

      * 用途

        * 暂存空间，例如用于基于磁盘的合并排序

        * 用作长时间计算崩溃恢复的检查点

        * Web服务器容器提供数据时，保存内容管理器容器提取的文件

          ```yaml
          apiVersion: v1
          kind: Pod
          metadata:
            name: test-pd
          spec:
            containers:
            - name: test-container
              image: nginx
              volumeMounts:
                - mountPath: /cache
                    name: cache-volume
            volumes:
              - name: cache-volume
                emptyDir: {}
          ```

    * hostPath：将主机节点的文件系统文件或目录挂载到集群中

      * 用途：

        * 运行时需要访问Docker内部的容器；使用 /var/lib/docker 的 hostPath

        * 在容器中运行cAdvisor；使用 /dev/cgroups 的 hostPath

        * 除了所需的path属性之外，用户还可以为hostPath 卷指定 type

          | 值                | 行为                                                         |
          | ----------------- | ------------------------------------------------------------ |
          |                   | 空字符串（默认）用于后兼容，这意味着在挂载hostPath卷之前不会执行任何检查 |
          | DirectoryOrCreate | 如果在给定的路径上没有任何东西存在，那么将根据需要在创建一个空目录权限为0755，与kubelet具有相同组和所有权 |
          | Directory         | 给定的路径下必须存在目录                                     |
          | FileOrCreate      | 如果不存在文件，会创建一个0644权限的文件，与kubelet具有相同组和所有权 |
          | File              | 给定的路径下必须存在文件                                     |
          | Socket            | 给定的路径下不许存在Unix套接字                               |
          | CharDevice        | 给定的路径下必须存在字符设备                                 |
          | BlockDevice       | 给定的路径下必须存在块设备                                   |

      * 注意

        * **由于每个及诶单的文件都不同，具有相同配置（例如从PodTempate创建）的Pod在不同节点上的行为可能会有所不同**

        * **当k8s按照计划添加资源感知调度时，将无法考虑hostPath使用的资源**

        * **在底层主机上创建的文件活目录只能由root写入。需要在特权容器中以root身份运行进程，或修改主机的读写权限**

          ```yaml
          apiVersion: v1
          kind: Pod
          metadata:
            name: test-pd
          spec:
            containers:
            - name: test-container
              image: nginx
              volumeMounts:
                - mountPath: /test-pd
                    name: test-volume
            volumes:
              - name: test-volume
                hostPath:
                # directory location on host
                path: /data
                # this field is optional
                type: Directory
          ```

          

  * Persistent Volume（持久卷）

    * PersistentVolume（PV）：**volume存储插件**，具有独立使用PV的Pod生命周期。此API包含存储实现的细节，即Nfs、iscsi货特定云厂商的存储系统

      * 静态PV：管理云可以创建一些PV，它带有可供集群用户使用的实际存储细节，存在于k8s API 中，可用于消费

      * 动态PV（不友好）：当管理员创建的静态PV都不匹配用户的PersistentVolumeClaim时，集群可能会尝试动态地为PVC创建卷。此配置基于`StorageClasses`:PVC必须请求[存储类]，并且管理员必须创建并配置该类才能进行动态创建。申明该类为""可以有效地仅用其他动态配置。

        * 要启用基于存储级别的动态存储配置，集群管理员需要启用API Server上的DefaultStorageClass[准入控制器]。例如，通过DefaultStorageClass位于API Server组件的--admission-control标志，使用分隔的有序值里表中，可以完成此操作

      * 绑定：master中的控制环路监视新的PVC，寻找匹配的PV（如果可能），并将它们绑定到一起。如果新的PVC动态调配PV，则该环路始终将PV绑定到PVC。否则，用户总会得到他们所请求的存储，但是容量可能超出要求的数量。一旦PV和PVC绑定后，`PersistentVolumeClain`绑定时排他性的，不管他们是如何绑定的。PVC根PV绑定时一对一映射。

      * 持久化卷申明的保护

        * PVC保护的目的是确保Pod正在使用的的PVC不会从系统中移除，移除会导致数据丢失
        * 当启用PVC保护alpha功能时；如果用户删除了一个pod正在使用的PVC，则该PVC不会立即删除。PVC删除将被推迟，直道PVC不再被任何Pod使用。

      * 持久化卷类型

        PersistentVolume类型以插件的形式实现。k8s支持以下插件类型：

        * GCEPersistentDisk  AWSElasticsBlockStore  AzureFile  AzureDisk  FC(Fibre Channel)
        * FlexVolume Flocker  Nfs  iSCSI  RBD(Ceph Block Device)  CephFS
        * Cinder(OpenStack Block storage)  Glusterfs  VsphereVolume  Qupbyte  Volumes
        * HostPath  VMware  Photon  Portworx  Volumes  ScaleIO  Volumes  StoreageOS

      * 持久化演示代码

        ```yaml
        apiVersion: v1
        kind: PersistentVolume
        metadata:
          name: pv0003
        spec:
          capacity:
            storage: 5Gi
          volumeMode: Filesystem
          accessMode:
            - ReadWriteOnce     # 只允许一个读写
          persistentVolumeReclaimPolicy: Recycle  # 回收策略
          storageClassName: slow  # 存储类名称划分存储情况等级（快/慢）
          mountOptions:
            - hard
            - nfsvers=4.1
          nfs:
            path: /tmp
            server: 172.17.0.2
        ```

      * PV访问模式

        *`PersistentVolume`可以以资源提供者支持的任何方式挂载到主机上。如下表所示，供应商具有不同的功能，每个PV的访问模式都将被设置为该类卷支持的特定模式。例如，NFS可以支持多个读/写客户端，但特定的NFS PV可以能以只读方式导出到服务器上。每个PV都有一套自己的用来描述特定功能的访问模式*

        * ReadWriteOnce -- 该卷可以被耽搁节点以读/写模式挂载
        * ReadOnlyMany -- 该卷可以被多个节点以只读模式挂载
        * ReadWriteMany -- 该卷可以被多个节点以读/写模式挂载

        命令行中，访问模式缩写为：

        * RWO - ReadWriteOnce

        * ROX - ReadOnlyMany

        * RWX - ReadWriteMany

          *一个卷只能使用一种访问模式挂载，即使它支持很多访问模式。例如，GCEPersistentDisk可以由单个节点为ReadWriteOnce模式挂载，或由多个节点以ReadOnlyMany模式挂载，但不能同时挂载*

        * 回收策略

          * Retain（保留）-- 手动回收

          * Recycle（回收）-- 基本擦除（rm -rf /thevolume/*）

          * Delete（删除）-- 关联的存储资产（例如：Aws EBS、GCE PD、Azure Disk和OpenStack Cinder卷）将被删除

            *当前，只有NFS(新版本废除回收)和HostPath支持回收策略。AWS EBS、GCE PD、Azure Disk和Cinder卷支持删除策略*

            | Volume插件           | ReadWriteOnce      | ReadOnlyMany       | ReadWriteMany                       |
            | -------------------- | ------------------ | ------------------ | ----------------------------------- |
            | AWSElasticBlockStore | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | AzureFile            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:                  |
            | AzureDisk            | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | CephFS               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:                  |
            | Cinder               | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | FC                   | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | FlexVolume           | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | Flocker              | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | GCEPersistentDisk    | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | Glusterfs            | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:                  |
            | HostPath             | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | iSCSI                | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | PhotonPersistentDisk | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |
            | Quobyte              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:                  |
            | NFS                  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:                  |
            | RBD                  | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | VsphereVolume        | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:(当Pod并列时有效)​ |
            | PortworxVolume       | :heavy_check_mark: | :heavy_minus_sign: | :heavy_check_mark:                  |
            | ScaleIO              | :heavy_check_mark: | :heavy_check_mark: | :heavy_minus_sign:                  |
            | StorageOS            | :heavy_check_mark: | :heavy_minus_sign: | :heavy_minus_sign:                  |

        * 状态

          卷可以处于以下几种状态

          * Avaiable（可用） -- 一块空闲资源还没有被任何声明绑定
          * Bound（已绑定）-- 卷已经被声明绑定
          * Released（已释放）-- 声明已被删除，但是资源还被集群重新声明
          * Failed（失败）-- 该卷的自动回收失败

          命令行回现实绑定到PV和PVC的名称

        * 持久化演示说明—Nfs

          * 安装Nfs服务器

            ```bash
            yum -y install nfs-common nfs-utils rpcbind
            mkdir /nfsdata
            chmod 666 /nfsdata
            cat /etc/exports
            		/nfsdata *(rw,no_root_squash,no_all_squash,sync)
            systemctl start rpcbind
            systemctl start nfs]
            
            # 客户端操作
            yum -y install nfs-utils rpcbind
            showmount -e 192.168.1.100
            mount -t nfs 192.168.1.100:/nfs /test
            ```

          * 部署PV

            ```yaml
            apiVersion: v1
            kind: PersistentVolume
            metadata:
            	name: nfsv1
            spec:
            	capacity:
            		storage: 1Gi
            	accessModes:
            		- ReadWriteOnce
            	persistentVolumeReclaimPolicy: Retain
            	storageClassName: nfs
            	nfs:
            		path: /data/nfs
            		server: 10.66.66.10
            ```

          * 创建服务并使用PVC

            ```yaml
            apiVersion: v1
            kind: Service
            metadata:
            	name: nginx
            	labels:
            		app: nginx
            spec:
            	ports:
              - port: 80
            		name: web
            	clusterIP: None
            	selector:
            		app: nginx
            ---
            apiVersion: apps/v1
            kind: StatefullSet
            metadata:
            	name: web
            spec:
            	selector:
            		matchLables:
            			app: nginx
            	serviceName: "nginx"
            	replicas: 3
            	template:
            		matadata:
            			labels:
            				app:nginx
            		spec:
            			containers:
            			- name: nginx
            				image: nginx
            				ports:
            				- containerPort: 80
            					name: web
            				volumeMounts:
            				- name: www
            					mountPath: /usr/share/nginx/html
               volumeClaimTemplates:
               - metadata:
               			name: www
               	spec:
               		accessModes: ["ReadWriteOnce"]
               		storageClassName: "nfs"
               		resources:
               			requests:
               				storage: 1Gi
            ```

    * PersistentVolumeClaim（PVC）：**用户存储请求，与Pod相似**。PVC消耗Pv的资源。Pod可以请求特定的资源（CPU和内存）。申明可以请求特定的大小和访问模式（如可以读/写一次或只读多次挂载）

