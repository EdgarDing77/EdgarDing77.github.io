---
title: Datadog Agent部署
date: 2021-09-14 17:47:15.454
updated: 2021-11-11 14:27:02.664
categories: [Cloud Native]
tags: [Cloud Native]
---

# Datadog Agent 部署

> Datadog Cluster Agent提供了一种简化的集中式方法来收集集群级别的监视数据。通过充当API服务器和基于节点的代理之间的代理，群集代理有助于减轻服务器负载。

原先的Node Agent都需要单独查询Kubernetes Api Server的数据，以收集有关整个集群的关键metadata。

![](https://cdn.jsdelivr.net/gh/edgarding77/images@latest/wallpaper/datadog.png)

## 部署

[https://www.datadoghq.com/blog/monitoring-kubernetes-with-datadog/](https://www.datadoghq.com/blog/monitoring-kubernetes-with-datadog/)

[https://medium.com/@while1eq1/installing-datadog-agent-in-kubernetes-8b9f6ca491c1](https://medium.com/@while1eq1/installing-datadog-agent-in-kubernetes-8b9f6ca491c1)







### 部署建议

部署agent的容器化版本，通过容器化的agent作为DaemonSet部署到集群中，可以确保集群中的每台主机上都可以运行该Agent的一个副本。

Datadog Cluster Agent：

- 通过充当在API server和基于节点的Agetns的proxy来减少Kubernetes API Server采集集群级数据的负载

- 通过减少基于节点代理所需的权限来提供额外的安全性

- 使用Datadog收集的任何指标启用Kubernetes工作负载的自动拓展

## 步骤

创建命名空间 datadog-namespace.yaml

```YAML
piVersion: v1
kind: Namespace
metadata:
  name: datadog
```

**在Agent 和 Cluster Agent中添加DD_DD_URL和DD_SITE字段。**

## Node Agent

组件：

- agent

- trace-agent

- process-agent

- system-probe

- security-agent

1、配置Agent权限

```Bash
kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrole.yaml"
# 为Pod中运行的进程提供标识，当Pod中的进程访问集群的时候，API Server将它们作为特定的服务账户进行身份验证。
kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/serviceaccount.yaml"

kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/rbac/clusterrolebinding.yaml"
```

> 注意：需要修改namesapce，默认为Default，可以改为datadog

2、创建一个secret包含Datadog API key，这个secret用于资源文件去部署Datadog Agent

```Bash
echo -n 'my_datadog_agent_api_key' | base64
```

```Bash
kubectl create secret generic datadog-agent --from-literal=api-key='<DATADOG_API_KEY>' --namespace="datadog"
```

注意：--from-literal=<key>=<value>提供Secret数据，如果有自定义namespace，需要进行修改。

#### NPM的开启

1、添加annotation在agent template

```YAML
spec:
    selector:
        matchLabels:
            app: datadog-agent
    template:
        metadata:
            labels:
                app: datadog-agent
            name: datadog-agent
            annotations:
                container.apparmor.security.beta.kubernetes.io/system-probe: unconfined
```

2、添加环境变量到Procss Agent容器中

```YAML
  # (...)
                  env:
                  # (...)
                      - name: DD_PROCESS_AGENT_ENABLED
                        value: 'true'
                      - name: DD_SYSTEM_PROBE_ENABLED
                        value: 'true'
                      - name: DD_SYSTEM_PROBE_EXTERNAL
                        value: 'true'
                      - name: DD_SYSPROBE_SOCKET
                        value: /var/run/sysprobe/sysprobe.sock
```

3、挂在到extra volumes到datadog—agent容器上

```YAML
 # (...)
        spec:
            serviceAccountName: datadog-agent
            containers:
                - name: datadog-agent
                  image: 'gcr.io/datadoghq/agent:latest'
                  # (...)
              volumeMounts:
                  - name: procdir
                    mountPath: /host/proc
                    readOnly: true
                  - name: cgroups
                    mountPath: /host/sys/fs/cgroup
                    readOnly: true
                  - name: debugfs
                    mountPath: /sys/kernel/debug
                  - name: sysprobe-socket-dir
                    mountPath: /var/run/sysprobe 
```

4、添加一个新的system-probe作为side car到Agent

```YAML
 # (...)
                - name: system-probe
                  image: 'datadog/agent:latest'
                  imagePullPolicy: Always
                  securityContext:
                      capabilities:
                          add:
                              - SYS_ADMIN
                              - SYS_RESOURCE
                              - SYS_PTRACE
                              - NET_ADMIN
                              - IPC_LOCK
                  command:
                      - /opt/datadog-agent/embedded/bin/system-probe
                  env:
                      - name: DD_SYSPROBE_SOCKET
                        value: /var/run/sysprobe/sysprobe.sock
                  resources:
                      requests:
                          memory: 150Mi
                          cpu: 200m
                      limits:
                          memory: 150Mi
                          cpu: 200m
                  volumeMounts:
                      - name: procdir
                        mountPath: /host/proc
                        readOnly: true
                      - name: cgroups
                        mountPath: /host/sys/fs/cgroup
                        readOnly: true
                      - name: debugfs
                        mountPath: /sys/kernel/debug
                      - name: sysprobe-socket-dir
                        mountPath: /var/run/sysprobe
```

5、volumes

```YAML
   volumes:
                - name: sysprobe-socket-dir
                  emptyDir: {}
                - name: debugfs
                  hostPath:
                      path: /sys/kernel/debug
```

```Bash

2021-04-28 08:24:38 UTC | SYS-PROBE | INFO | (cmd/system-probe/loader.go:50 in Register) | module: network_tracer started
2021-04-28 08:24:38 UTC | SYS-PROBE | INFO | (cmd/system-probe/modules/tcp_queue_tracer.go:21 in func4) | TCP queue length tracer disabled
2021-04-28 08:24:38 UTC | SYS-PROBE | INFO | (cmd/system-probe/modules/oom_kill_probe.go:20 in func2) | OOM kill probe disabled
2021-04-28 08:24:40 UTC | SYS-PROBE | ERROR | (cmd/system-probe/loader.go:44 in Register) | error registering HTTP endpoints for module `security_runtime` error: failed to init probe: failed to init manager: couldn't load eBPF programs: map syscalls: map create: invalid argument
```

## Cluster Agent

1、安装Datadog Cluster Agent

2、配置Agent与Datadog Cluster Agent的通信

### 配置Datadog Cluster Agent

#### 配置RBAC 权限

> 当使用Cluster Agent与Kubernetes API Server通信的时候，Node Agent将会失效，只有Cluster Agent可以进行

```Bash
# 若配置上面node-rbac.yaml 将会更新node 的clusterRole，可以忽略node-rbac
kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/rbac.yaml"
# 
kubectl apply -f "https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/cluster-agent-rbac.yaml"
```

将会创建适当的`ServiceAccount`、`ClusterRole`和`ClusterRoleBinding`为Cluster Agent，同时更新`ClusterRole`对于Node Agent。



#### Cluster Agent 与 Agent的通信安全

> 用于保护Cluster-Agent和Node-Agent的通信安全 —— datadog-cluster-agent

注意：token必须至少32个字符长

```Bash
 echo -n 'my_datadog_cluster_agent_token' | base64
 bXlfZGF0YWRvZ19jbHVzdGVyX2FnZW50X3Rva2Vu 
```

```Bash
kubectl create secret generic datadog-cluster-agent --from-literal=token='bXlfZGF0YWRvZ19jbHVzdGVyX2FnZW50X3Rva2Vu' --namespace="datadog"  
```

创建一个name=datadog-cluster-agent的secret

`DD_CLUSTER_AGENT_AUTH_TOKEN`将会在Cluter Agent manifest中使用

注意：需要被设置在Cluster Agent和Node Agent的manifest文件中

使用配置文件创建样例：secrets.yaml

```YAML
# secret-api-key.yaml
apiVersion: v1
kind: Secret
metadata:
  name: datadog
  labels: {}
type: Opaque
data:
  api-key: PUT_YOUR_BASE64_ENCODED_API_KEY_HERE
--- 
# secret-cluster-agent-token.yaml
apiVersion: v1
kind: Secret
metadata:
  name: datadog-cluster-agent
  labels: {}
type: Opaque
data:
  token: PUT_A_BASE64_ENCODED_RANDOM_STRING_HERE
```

#### 创建Cluster Agnet和它的Service

- [`agent-services.yaml`](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/agent-services.yaml)[: The Cluster Agent Service manifest](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/agent-services.yaml)

- [`secrets.yaml`](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/secrets.yaml)[: The secret holding the Datadog API key](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/secrets.yaml)

- [`cluster-agent-deployment.yaml`](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/cluster-agent-deployment.yaml)[: Cluster Agent manifest](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/cluster-agent-deployment.yaml)

- [`install_info-configmap.yaml`](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/install_info-configmap.yaml)[: Install Info Configmap](https://raw.githubusercontent.com/DataDog/datadog-agent/master/Dockerfiles/manifests/cluster-agent/install_info-configmap.yaml)

1、在secrets.yaml，替换Datadog API key —— datadog

```Bash
echo -n 'my_datadog_cluster_agent_api' | base64
bXlfZGF0YWRvZ19jbHVzdGVyX2FnZW50X2FwaQ== 
```

```Bash
kubectl create secret generic datadog --from-literal=api-key='bXlfZGF0YWRvZ19jbHVzdGVyX2FnZW50X2FwaQ==' --namespace="datadog"
```

2、在cluster-agent-deployment.yaml中设置token根据创建方式。

3、依次run agent-services、secrets、install_info-configmap

4、最后run datadog Cluster-Agent-deployment

5、在安装完Datadog CLuster Agent后，配置Datadog Agnet去与Datadog Cluster Agent进行通信。

注意：修改deployment中的容忍度tolerations

### 配置Datadog Agent（Node Agent）

#### 为Node-Base Agents 安装RBAC权限

agent-rbac.yaml

#### 启用Datadog Agent

1、下载daemonset.yaml

2、在daemonset，取代DD_SITE的默认值，（用DD_DD_URL代替要Forwarder的地方）

3、在daemonset.yaml中设置token，通过自己创建secret的方式

4、在daemonset.yaml，检查`DD_CLUSTER_AGENT_ENABLED`是否是true

5、（可选）配置一些单独的环境

6、运行

注意：daemonset和deployment中的image分别需要改为：
- "[harbor.od.com/datadog/cluster-agent:latest](http://harbor.od.com/datadog/cluster-agent:latest)"
- "[harbor.od.com/datadog/agent:latest](http://harbor.od.com/datadog/agent:latest)"

## 问题

### token和API key未启用

添加optional： true

```YAML

        env:
        - name: DD_API_KEY
          valueFrom:
            secretKeyRef:
              name: "datadog"
              key: api-key
              optional: true

```

### 删除命名空间失效

查找字段“finalizers”，去除“kubernetes”

```Bash
kubectl delete ns <namespace>

kubectl proxy
kubectl get ns <namesapce> -o json > tmp.json


curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/datadog-agent/finalize
```

### 修复节点不会调度问题

判断节点是否开启

```Bash

[root@master-80 main]# kubectl get nodes
NAME      STATUS                     ROLES    AGE   VERSION
node-81   Ready,SchedulingDisabled   <none>   88d   v1.18.15
node-82   Ready                      <none>   88d   v1.18.15

```

```Bash
kubectl uncordon <node-name>
```

```YAML
    tolerations:
      - key: CriticalAddonsOnly
        operator: Exists
      - effect: NoSchedule
        operator: Exists
      - effect: NoExecute
        operator: Exists
```

修改tolerations

### Error: secret "datadog-appkey" not found

删除，否则会导致容器创建失败

```YAML
        - name: DD_APP_KEY
          valueFrom:
            secretKeyRef:
              name: "datadog-agent"
              key: app-key
              optional: true
```

### ？image GC failed

```Bash
{
        "apiKey":"",
        "internalHostname":"node-82",
        "events":{
                "kubernetes":[
                        {
                                "aggregation_key":"kubernetes_apiserver:node-81",
                                "event_type":"kubernetes_apiserver",
                                "msg_title":"Events from the Node node-81",
                                "host":"node-81",
                                "msg_text":"%%% \n4541 **ImageGCFailed**: (combined from                                                                                                                           similar events): wanted to free 15911626342 bytes, but freed 0 bytes space with errors                                                                                                                           in image deletion: rpc error: code = Unknown desc = Error response from daemon: conflict                                                                                                                          : unable to remove repository reference \"nginx_new:latest\" (must force) - container 68                                                                                                                          ca1f2b153a is using its referenced image 264637614cff\n \n _Events emitted by the kubele                                                                                                                          t seen at 2021-04-22 03:39:29 +0000 UTC since 2021-04-06 08:23:15 +0000 UTC_ \n\n %%%",
                                "priority":"normal",
                                "source_type_name":"kubernetes",
                                "alert_type":"warning",
                                "timestamp":1619062769,
                                "tags":[
                                        "kubernetes_kind:Node",
                                        "name:node-81",
                                        "source_component:kubelet"
                                ]
                        }
                ]
        }
}
```

参考：[https://github.com/kubernetes/kubernetes/issues/71869](https://github.com/kubernetes/kubernetes/issues/71869)

```Bash
# 以清理出足够的磁盘容量
kubectl drain --delete-local-data --ignore-daemonsets $NODE_IP && kubectl uncordon $NODE_IP 
```

### ？node Agent  err collecting

`cat /var/log/datadog/agent.log | grep "metadata-collector"`

```Bash
| CORE | WARN | (pkg/tagger/local/tagger.go:251 in Tag) | error collecting from kube-metadata-collector: couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.81:10250    /pods: Unauthorized

error collecting from kube-metadata-collector: couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.81:10250/pods: Unauthorized

```

猜测是因为安装了cluster-agent的缘故导致agent向API Server获取Kubernetes集群的信息失败。

### ？providerID not found

```Bash
| CLUSTER | WARN | (pkg/collector/corechecks/cluster/kubernetes_eventbundle.go:165 in getHostProviderID) | ProviderID not found

```

[https://stackoverflow.com/questions/60288304/what-is-the-way-to-make-kubernetes-nodes-have-providerid-spec-after-creation](https://stackoverflow.com/questions/60288304/what-is-the-way-to-make-kubernetes-nodes-have-providerid-spec-after-creation)

> provider-id string. Unique identifier for identifying the node in a machine database, i.e cloud provider. (DEPRECATED: This parameter should ...

### ？not list resource “podsecuritypolicies”

```Bash
      Error: unable to list Kube resources:'policy/v1beta1, Resource=podsecuritypolicies', ns:'' name:'', err: podsecuritypolicies.policy is forbidden: User "system:serviceaccount:datadog:datadog-cluster-agent" cannot list resource "podsecuritypolicies" in API group "policy" at the cluster scope
      No traceback
```

### ？Error while processing transaction

```Bash
 CLUSTER | ERROR | (pkg/forwarder/worker.go:178 in process) | Error while processing transaction: error while sending transaction, rescheduling it: Post "http://10.230.4.20:12345/intake/?api_key=*************************2FwaQ==": dial tcp 10.230.4.20:12345: connect: connection refuse
```

### node agent

agent

```Bash

2021-04-25 09:23:03 UTC | CORE | WARN | (pkg/tagger/local/tagger.go:251 in Tag) | error collecting from kubelet: couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.82:10250/pods: Unauthorized
2021-04-25 09:23:03 UTC | CORE | WARN | (pkg/tagger/local/tagger.go:206 in pull) | couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.82:10250/pods: Unauthorized
```

trace-agent

```Bash
2021-04-25 09:23:22 UTC | TRACE | ERROR | (pkg/tagger/collectors/kubernetes_main.go:63 in Detect) | Could not initialise the communication with the cluster agent: temporary failure in clusterAgentClient, will retry later: cannot get a cluster agent endpoint for kubernetes service DATADOGT_CLUSTER_AGENT, env DATADOGT_CLUSTER_AGENT_SERVICE_HOST is empty
2021-04-25 09:23:22 UTC | TRACE | WARN | (pkg/tagger/local/tagger.go:206 in pull) | couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.82:10250/pods: Unauthorized
```

process-agent

```Bash

2021-04-25 09:23:34 UTC | PROCESS | ERROR | (pkg/forwarder/worker.go:174 in process) | Too many errors for endpoint 'https://process.datadoghq.com/api/v1/collector': retrying later
2021-04-25 09:23:37 UTC | PROCESS | WARN | (pkg/tagger/local/tagger.go:206 in pull) | couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.82:10250/pods: Unauthorized
2021-04-25 09:23:41 UTC | PROCESS | ERROR | (pkg/process/util/containers.go:64 in GetContainers) | Cannot list containers via kubelet: could not get pod list: couldn't fetch "podlist": unexpected status code 401 on https://10.230.4.82:10250/pods: Unauthorized

```

sysem-probe

```Bash
2021-04-25 09:17:31 UTC | SYS-PROBE | INFO | (cmd/system-probe/modules/network_tracer.go:129 in logRequests) | Got request on /connections?client_id=32460 (count: 6580): retrieved 752 connections in 73.063134ms
2021-04-25 09:18:21 UTC | SYS-PROBE | INFO | (pkg/network/dns_snooper.go:214 in logDNSStats) | DNS Stats. Queries :0, Successes :0, Errors: 752
```

security-agent

```Bash
2021-04-25 09:29:18 UTC | SECURITY | ERROR | (pkg/forwarder/worker.go:178 in process) | Error while processing transaction: error while sending transaction, rescheduling it: Post "https://7-26-0-app.agent.datadoghq.com/intake/?api_key=*************************2FwaQ==": dial tcp: lookup 7-26-0-app.agent.datadoghq.com: Temporary failure in name resolution
2021-04-25 09:29:23 UTC | SECURITY | ERROR | (pkg/forwarder/worker.go:174 in process) | Too many errors for endpoint 'https://7-26-0-app.agent.datadoghq.com/api/v1/check_run?api_key=*************************2FwaQ==': retrying later

```

## 基础配置文件

[daemonset - 基础版](https://www.wolai.com/rvm8w1Cva9uqvEW119W5X4)

[cluster-agent-deployment](https://www.wolai.com/4RPoft7pwFUkve3v8FESUQ)

[cluster-agent-rbac](https://www.wolai.com/v5nKaXb6jHKWP31ykbXJPW)

[rbac](https://www.wolai.com/qN9sMrenGzDjyaFi2SCoDz)

## 诊断

**参考**：



### Cluster Agent Troubleshooting

先进入到cluster-agent容器内

```Bash
kubectl exec -it <DATADOG_CLUSTER_AGENT_POD_NAME> bash
```

执行`datadog-cluster-agent metamap`，查看Datadog Cluster Agent服务于哪些集群级别的元数据。

```YAML
root@datadog-cluster-agent-6db94974f9-mxtl5:/# datadog-cluster-agent metamap
error: Error creating expvar server on port 5000: listen tcp 0.0.0.0:5000: bind: address already in use
2021-04-22 03:10:22 UTC | CLUSTER | INFO | (pkg/util/log/log.go:526 in func1) | Features detected from environment: map[3:{}]
2021-04-22 03:10:22 UTC | CLUSTER | ERROR | (pkg/util/log/log.go:551 in func1) | Error creating expvar server on port 5000: listen tcp 0.0.0.0:5000: bind: address already in use
2021-04-22 03:10:22 UTC | CLUSTER | WARN | (pkg/util/log/log.go:541 in func1) | Agent configuration relax permissions constraint on the secret backend cmd, Group can read and exec
===============
Metadata Mapper
===============

Node detected: node-81

  - Namespace: istio-system
      - Pod: grafana-775698c4c8-6t46s
        Services: [grafana]
      - Pod: kiali-74bcfc7575-cglnt
        Services: [kiali]

Node detected: node-82

  - Namespace: datadog
      - Pod: datadog-cluster-agent-6db94974f9-mxtl5
        Services: [datadog-cluster-agent datadog-cluster-agent-metrics-api]

  - Namespace: elk-flow-collector
      - Pod: elasticsearch-0
        Services: [elasticsearch]
      - Pod: kibana-d5f5889b9-4dfq8
        Services: [kibana]
      - Pod: logstash-67468fd8bb-cswn4
        Services: [logstash]

  - Namespace: flow-aggregator
      - Pod: flow-aggregator-849d4865b8-b2v7b
        Services: [flow-aggregator]

  - Namespace: istio-system
      - Pod: jaeger-f5c857df9-9gb5k
        Services: [jaeger-collector tracing zipkin]
      - Pod: prometheus-84797dd77c-8p4v8
        Services: [prometheus]

  - Namespace: kube-system
      - Pod: antrea-controller-6dc8cb4469-85cgx
        Services: [antrea]
      - Pod: coredns-6bf74754-cv7j7
        Services: [coredns]
      - Pod: coredns-6bf74754-dq9z7
        Services: [coredns]

```

验证Datadog Cluster是否正在被查询

```Bash
tail -f /var/log/datadog/cluster-agent.log
```

确保如下env为true

- Leader election: `DD_LEADER_ELECTION`

- Event collection: `DD_COLLECT_KUBERNETES_EVENTS`

同时RBAC的verbs（watch events），通过以下命令检查

```Bash
datadog-cluster-agent status
```

### Node Agent

确保Datadog Cluster Agent处于running并可用`agent status`

```Bash
=====================
Datadog Cluster Agent
=====================

  - Datadog Cluster Agent endpoint detected: https://192.168.84.76:5005
  Successfully connected to the Datadog Cluster Agent.
  - Running: 1.11.0+commit.4eadd95

```

确保Cluster Agent service创建在Agent pod之前以至于DNS在环境变量中是可用的

```Bash
env | grep DATADOG_CLUSTER_AGENT | sort
echo ${DD_CLUSTER_AGENT_AUTH_TOKEN} 
```

- [ ] 验证node agent正在使用Cluster Agent作为tag provider

```Bash
cat /var/log/datadog/agent.log | grep "metadata-collector"
```

