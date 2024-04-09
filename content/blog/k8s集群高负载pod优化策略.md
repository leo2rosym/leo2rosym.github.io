---
date: 2024-04-09 09:00:00
tags:
  - kubernetes
title: k8s集群高负载pod优化策略
---

### 大量Evicted状态的pod如何处理
随着业务的激增，集群中某个服务的日调用次数达到100亿的量级，这些高负载pod占用了node节点上绝大部分的cpu，导致节点上其他的pod访问延迟也开始增大，被驱逐的pod越来越多。
```
Status:           Failed
Reason:           Evicted
```
### 批量清理Evicted状态的pod
```
from kubernetes import client, config
import time

# Configure the client to use the cluster's configuration
config.load_kube_config()

# Create an instance of the CoreV1Api
v1 = client.CoreV1Api()

# Define the namespace
namespace = 'prod'

# Get the list of all pods in the specified namespace
pods = v1.list_namespaced_pod(namespace)

# Iterate over the list of pods and delete evicted ones
for pod in pods.items:
    if pod.status.phase == 'Failed' and pod.status.reason == 'Evicted':
        print(f"Deleting evicted pod: {pod.metadata.name}")
        v1.delete_namespaced_pod(pod.metadata.name, namespace)
        time.sleep(2)  # Wait for 2 seconds before deleting the next pod

print("All evicted pods have been deleted.")
```

### 更新策略和拓扑分布约束
```
  selector:
    matchLabels:
      app: __platform__-__app__
  strategy:
    rollingUpdate:
      maxSurge: 10%
      maxUnavailable: 10%
    type: RollingUpdate      
  template:
    metadata:
      labels:
    spec:
      imagePullSecrets:
        - name: <secret>
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: kubernetes.io/hostname
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: __platform__-__app__      
      containers:
        - name: __platform__-__app__
          image: <image>
```

### 通过污点容忍策略将高负载的pod调度到单独的一组cpu优化型node节点
| _kubectl label nodes _<node-name>_ node-role=cpu-worker

kubectl taint node _<node-name>_ node-role=cpu-worker:NoSchedule_ |
| --- |

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: __platform__-__app__-deployment
  namespace: prod
  labels:
    app: __platform__-__app__
spec:
  replicas: __replicas__
  selector:
    matchLabels:
      app: __platform__-__app__
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: __platform__-__app__
        application: __platform__-__app__
      annotations:
        prometheus.io/scrape: 'true'
        prometheus.io/path: '/actuator/prometheus'
        prometheus.io/port: '8080'
    spec:
      imagePullSecrets:
        - name: <secret>
      topologySpreadConstraints:
        - maxSkew: 2
          topologyKey: kubernetes.io/hostname
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: __platform__-__app__
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
              - matchExpressions:
                  - key: node-role
                    operator: In
                    values:
                      - cpu-worker
      tolerations:
        - key: "node-role"
          operator: "Equal"
          value: "cpu-worker"
          effect: "NoSchedule"
      containers:
        - name: __platform__-__app__
          image: <image>
          imagePullPolicy: Always
          lifecycle:
            preStop:
              exec:
                command: ["sh","-c","/data/apps/hook_prestop.sh"]
          env:
            - name: POD_ID
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
          volumeMounts:
            - name: <volume>
              mountPath: /data2
          resources:
            requests:
              cpu: "3"
              memory: "5Gi"
            limits:
              cpu: "5"
              memory: "7Gi"
          ports:
            - containerPort: 8080
          livenessProbe:
            tcpSocket:
              port: 8080
            initialDelaySeconds: 50
            timeoutSeconds: 2
            periodSeconds: 5
            failureThreshold: 3
          readinessProbe:
            tcpSocket:
              port: 8080
            initialDelaySeconds: 50
            periodSeconds: 5
            timeoutSeconds: 1
            successThreshold: 1
      volumes:
        - name: <volume>
          persistentVolumeClaim:
            claimName: <volume>

```


