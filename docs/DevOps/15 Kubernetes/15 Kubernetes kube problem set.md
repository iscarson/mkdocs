问题集合

> * 0/1 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate.
>   * 意思是节点有了污点无法调度，出于安全考虑k8s不允许在master节点部署pod
>   * 解决方案：
>     * ```kubectl get no -o yaml | grep taint -A 5```**(查看节点是否可调度)**
>     * ```kubectl taint nodes --all node-role.kubernetes.io/master-```

