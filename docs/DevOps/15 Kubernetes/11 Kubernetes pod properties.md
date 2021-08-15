
# 必备字段

| 参数名                  | 字段类型 | 说明                                                         |
| ----------------------- | -------- | ------------------------------------------------------------ |
| version                 | String   | 这里是指k8s api的版本，目前基本是v1,可以使用kubectl api-versions命令查询 |
| kind                    | String   | yaml文件定义的资源类型和角色,比如:Pod                        |
| metadata                | Object   | 元数据对象,固定值就写metadata                                |
| metadata.name           | String   | 元数据对象名称,自定义名字如Pod名字                           |
| metadata.namespace      | String   | 元数据对象的命名空间,自定义                                  |
| spec                    | Object   | 详细定义对象,固定值就写Spec                                  |
| spec.containers[]       | list     | Spec对象的容器列表定义，是个列表                             |
| spec.containers[].name  | String   | 容器的名字                                                   |
| spec.containers[].image | String   | 镜像的名称                                                   |
# 主要对象类型

| 参数名                                      | 字段类型 | 说明                                                         |
| ------------------------------------------- | -------- | ------------------------------------------------------------ |
| spec.containers[].name                      | String   | 定义容器的名称                                               |
| spec.containers[].image                     | String   | 定义要使用的镜像名称                                         |
| spec.containers[].imagePullPolicy           | String   | 定义拉取镜像的策略,有Always、Nerver、ifNotPresent三个值可选 <br />（1）Always:意思是每次都尝试重新拉取镜像<br />（2）Never:表示仅使用本地镜像<br />（3）ifNotpresent:如果本地有就使用本地镜像,没有就拉取在线镜像。默认值为Always。 |
| spec.containers[].command[]                 | List     | 指定容器启动命令，因为是数组可以指定多个，不指定则使用镜像打包时的启动命令 |
| spec.containers[].args[]                    | List     | 指定容器启动命令参数,可以指定多个                            |
| spec.containers[].workingDir                | String   | 指定容器的工作目录                                           |
| spec.containers[].volumeMounts[]            | List     | 指定容器内部的存储卷配置                                     |
| spec.containers[].volumeMounts[].name       | String   | 指定可以被容器挂在的存储卷名称                               |
| spec.containers[].volumeMounts[].mountPath  | String   | 指定可以被容器挂载的存储卷路径                               |
| spec.containers[].volumeMounts[].readOnly   | String   | 设置存储卷路径的读写模式,ture或者是false 默认为读写          |
| spec.containers[].port[]                    | List     | 指定容器需要用到的端口列表                                   |
| spec.containers[].port[].name               | String   | 指定端口名称                                                 |
| spec.containers[].port[].containerPort      | String   | 指定容器需要监听的端口                                       |
| spec.containers[].port[].hostPort           | String   | 指定容器所在主机需要监听的端口号,默认跟上面containerPort相同，注意设置了hostPort同一台主机无法启动该容器的相同副本（同一台主机不能使用相同端口） |
| spec.containers[].port[].protocol           | String   | 指定端口协议,支持TCP和UDP,默认时Tcp                          |
| spec.containers[].env[]                     | List     | 指定容器运行前需设置的环境变量列表                           |
| spec.containers[].env[].name                | String   | 指定环境变量名称                                             |
| spec.containers[].env[].value               | String   | 指定环境变量值                                               |
| spec.containers[].resources                 | Object   | 指定资源限制和资源请求的值（这里开始就是设置容器的资源上线） |
| spec.containers[].resources.limit           | Object   | 指定容器运行时资源的运行上限                                 |
| spec.containers[].resources.limit.cpu       | String   | 指定CPU的限制,单位为core数,将用于docker run —cpu-shares参数  |
| spec.containers[].resources.limit.memory    | String   | 指定Mem内存的限制,单位为Mib,Gib                              |
| spec.containers[].resources.requests        | Object   | 指定容器启动和调度时的限制设置                               |
| spec.containers[].resources.requests.cpu    | String   | cpu请求,单位为core数,容器启动时初始化可用数量                |
| spec.containers[].resources.requests.memory | String   | 内存请求,单位为Mib,Gib,容器启动的初始化可用数量              |
# 额外的参数选项

| 参数名                | 字段类型 | 说明                                                         |
| --------------------- | -------- | ------------------------------------------------------------ |
| spec.restartPolicy    | String   | 定义Pod的重启策略，可选值为always、onFalure,默认值为Always.<br />1.Always:Pod一旦终止运行,则无论容器是如何终止的,kubelet服务都将重启它<br />2.OnFailure: 只有Pod以非零退出代码终止时，kubelet才会重启该容器。如果容器正常结束（退出代码为0），则kubelet不会重启它。<br />3.Nerver:Pod终止后,kubelet将退出代码报告给master，不会重启该Pod |
| spec.nodeSelector     | Object   | 定义Node的Label过滤标签,以key:value格式指定                  |
| spec.imagePullSecrets | Object   | 定义pull镜像时使用secret名称,以name:secretey格式指定         |
| spec.hostNetwork      | Boolean  | 定义是否使用主机模式网络，默认为false。设置true表示使用宿主机网络,不实用docker网桥，同时设置了true将无法在同一台宿主机启动第二个副本。 |