prometheus-operator

## 项目说明

    本项目为 prometheus-operator 其他切出的分支, 用于配置 Prometheus 在 k8s 上部署Prometheus, 完成部署后自动监控k8s集群将进行我绘制grafana图形

    本项目基于 Kubernetes 1.20 版本之上进行部署, 包含 Prometheus(高可用, 持久化), Alertmanager(高可用), Grafana(持久化), NFS持久化存储类

    在原有的项目修改好以下文件：
        修改：     manifests/alertmanager-alertmanager.yaml   # 匹配 AlertmanagerConfig 的标签
        修改：     manifests/alertmanager-service.yaml          # 修改端口类型
        修改：     manifests/grafana-deployment.yaml            # 持久化存储 
        修改：     manifests/grafana-service.yaml               # 修改端口类型
        修改：     manifests/prometheus-prometheus.yaml         # 持久化存储 
        修改：     manifests/prometheus-service.yaml            # 修改端口类型

        新增：     manifests/kube-controllermanager-schedule-svc-ep.yaml    # 配置监控 controller-manager 和 scheduler
        新增：     manifests/other/alertmanager-config.yaml                 # 增加自定义报警接收类型
        新增：     manifests/other/etcdRules.yaml                           # 配置监控etcd的规则 
        新增：     manifests/other/serviceMonitorEtcd.yaml                  # etcd的ServiceMonitor内容
        新增：     manifests/other/etcd-Service.yaml                        # etcd 的 service 和 ep
        新增：     manifests/storage/nfs-storageclass.yaml                  # nfs storageclass

## 配置
    1. 配置nfs storageclass, 修改nfs服务器地址, 前提是需要部署好nfs存储服务器, 修改 nfs-storageclass.yaml 配置文件 

    2. 监控etcd集群时, 需要修改etcd集群服务器的地址, 修改 etcd-Service.yaml 配置文件 

    3. 监控controller-manager 和 scheduler 服务时, 需要修改systemd的监控地址及增加如下二个参数

        kube-controller-manager.services
            --authentication-kubeconfig=/etc/kubernetes/kube-controller-manager.kubeconfig # 指定 kubeconfig 配置文件路径 
            --authorization-kubeconfig=/etc/kubernetes/kube-controller-manager.kubeconfig  # 指定 kubeconfig 配置文件路径 
        
        kube-scheduler.service
            --authentication-kubeconfig=/etc/kubernetes/kube-scheduler.kubeconfig # 指定 kubeconfig 配置文件路径 
            --authorization-kubeconfig=/etc/kubernetes/kube-scheduler.kubeconfig # 指定 kubeconfig 配置文件路径 
        
        二个配置文件的监控地址需要外网可以访问才行， 建议使用：
            --bind-address=0.0.0.0
    
    4. 配置报警接收类型
        修改alertmanager-config.yaml, 操作完成之后进行apply即可


## 部署
    ` kubectl apply -f storage/ -f other/ -f setup/ -f .   # 此操作一次可能无法成功, 需要执行2到3次`

    


