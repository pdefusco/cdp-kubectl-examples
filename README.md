# cdp-kubectl-examples



### AI Registry

```
% kubectl get namespaces --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml

NAME                   STATUS   AGE
default                Active   3d6h
istio-system           Active   3d6h
knox                   Active   3d6h
kube-node-lease        Active   3d6h
kube-public            Active   3d6h
kube-system            Active   3d6h
kubernetes-dashboard   Active   3d6h
liftie-core-tools      Active   3d6h
logging                Active   3d6h
metering               Active   3d6h
mlx                    Active   3d6h
```

```
% kubectl get pods -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml

NAME                                 READY   STATUS                   RESTARTS       AGE
model-registry-db-0                  1/1     Running                  0              3d6h
model-registry-v1-7cb74845c6-w25v9   2/2     Running                  1 (3d6h ago)   3d6h
model-registry-v2-7cfc98fcb5-4r826   0/2     ContainerStatusUnknown   2              26h
model-registry-v2-7cfc98fcb5-9tv4f   2/2     Running                  0              132m
model-registry-v2-7cfc98fcb5-jhcvr   0/2     ContainerStatusUnknown   2              3d6h
model-registry-v2-7cfc98fcb5-v5rdp   2/2     Running                  0              3d6h
```

```
% kubectl describe pod model-registry-v1-7cb74845c6-w25v9 -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
Name:             model-registry-v1-7cb74845c6-w25v9
Namespace:        mlx
Priority:         0
Service Account:  sa-cdsw-unprivileged
Node:             ip-[].ec2.internal/[].68
Start Time:       Mon, 06 Oct 2025 12:41:06 -0700
Labels:           app=model-registry-v1
                  modelregistry=caller
                  pod-template-hash=7cb74845c6
                  role=model-registry-v1
                  version=test

```

### AI Inference Service

```
% kubectl get namespaces --kubeconfig=inference-cluster-kubeconfig.yaml             

NAME                      STATUS   AGE
cert-manager              Active   4h10m
cml-serving               Active   4h9m
default                   Active   3d7h
external-dns              Active   3d6h
istio-system              Active   3d6h
knative-serving           Active   4h11m
knox                      Active   3d6h
knox-operator-system      Active   3d6h
kserve                    Active   4h10m
kube-node-lease           Active   3d7h
kube-public               Active   3d7h
kube-system               Active   3d7h
kubernetes-dashboard      Active   3d7h
liftie-clusters-monitor   Active   3d7h
liftie-core-tools         Active   3d7h
logging                   Active   3d6h
metering                  Active   3d6h
mlx                       Active   3d6h
monitoring                Active   3d6h
serving-default           Active   4h8m
yunikorn                  Active   3d6h
```

```
% kubectl get pods -n kserve --kubeconfig=inference-cluster-kubeconfig.yaml

NAME                                         READY   STATUS    RESTARTS   AGE
kserve-controller-manager-7dcc78998c-8mg8q   2/2     Running   0          4h11m
```

```
% kubectl get pods -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
NAME                             READY   STATUS    RESTARTS   AGE
api-fdf646db9-f4fd9              2/2     Running   0          4h10m
archiver-58c7c4f99d-jqbv7        2/2     Running   0          4h10m
authorizer-7457fc8d66-vlfdw      3/3     Running   0          4h10m
usage-reporter-7dffd87f5-7lrh6   3/3     Running   0          4h10m
```

Model endpoint is located in "serving-default" namespace.

```
% kubectl get pods -n serving-default --kubeconfig=inference-cluster-kubeconfig.yaml

NAME                                                              READY   STATUS    RESTARTS   AGE
llama-33-70b-instruct-endpoint-predictor-00001-deployment-bv84m   0/3     Pending   0          62m
```

Troubleshoot Connectivity

```
kubectl get pods -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl get configmaps -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl logs -f api-fdf646db9-f4fd9 -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl exec -it api-fdf646db9-f4fd9 -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml -- bash
```

```
kubectl get configmaps -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl get configmap -o yaml api-config -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl edit configmap api-config -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl get pods -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl delete pod api-fdf646db9-f4fd9 -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl get pods -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```

```
kubectl logs -f api-fdf646db9-f97l9 -n cml-serving --kubeconfig=inference-cluster-kubeconfig.yaml
```


### Old Registered Model but no longer compatible with current version of CAII

```
❯ k get pods -n serving-default
NAME                                                      READY   STATUS    RESTARTS   AGE
xgboost-clf-predictor-00001-deployment-59969cb55d-hzr66   3/3     Running   0          15d
```

As there are two trying to get deployed I scaled up the deployment for one of those manually

```
❯ k get deployment -n serving-default
NAME                                             READY   UP-TO-DATE   AVAILABLE   AGE
llama-endpoint-paul-predictor-00001-deployment   0/0     0            0           19h
llama-endpoint-predictor-00001-deployment        0/0     0            0           21h
xgboost-clf-predictor-00001-deployment           1/1     1            1           15d
```

```
❯ k -n serving-default scale deployment/llama-endpoint-paul-predictor-00001-deployment --replicas=1
deployment.apps/llama-endpoint-paul-predictor-00001-deployment scaled
```

```
❯ k get deployment -n serving-default
NAME                                             READY   UP-TO-DATE   AVAILABLE   AGE
llama-endpoint-paul-predictor-00001-deployment   0/1     1            0           19h
llama-endpoint-predictor-00001-deployment        0/0     0            0           21h
xgboost-clf-predictor-00001-deployment           1/1     1            1           15d
```

We can see the pods but its in init state since 8min

```
❯ k -n serving-default get pods
NAME                                                              READY   STATUS     RESTARTS   AGE
llama-endpoint-paul-predictor-00001-deployment-767c99487b-7tz6b   0/3     Init:1/2   0          8m8s
xgboost-clf-predictor-00001-deployment-59969cb55d-hzr66           3/3     Running    0          15d
```

Latest event from the pod

```
Events:
  Type    Reason             Age   From      Message
  ----    ------             ----  ----      -------
  Normal  Scheduling         12m   yunikorn  serving-default/llama-endpoint-paul-predictor-00001-deployment-767c99487b-7tz6b is queued and waiting for allocation
  Normal  Scheduled          12m   yunikorn  Successfully assigned serving-default/llama-endpoint-paul-predictor-00001-deployment-767c99487b-7tz6b to node ip-10-10-3-26.ec2.internal
  Normal  PodBindSuccessful  12m   yunikorn  Pod serving-default/llama-endpoint-paul-predictor-00001-deployment-767c99487b-7tz6b is successfully bound to node ip-10-10-3-26.ec2.internal
  Normal  Pulled             12m   kubelet   Container image "container.repository.cloudera.com/cloudera_thirdparty/gloo-mesh/istio-e038d180f90a/proxyv2:1.25.1-solo-fips-distroless" already present on machine
  Normal  Created            12m   kubelet   Created container: istio-validation
  Normal  Started            12m   kubelet   Started container istio-validation
  Normal  Pulled             11m   kubelet   Container image "container.repository.cloudera.com/cloudera/cml-serving/kserve_storage_initializer:1.7.0-b23" already present on machine
  Normal  Created            11m   kubelet   Created container: storage-initializer
  Normal  Started            11m   kubelet   Started container storage-initializer
```

The model is actively downloading. While this download is active, we wanted to know what is the reason of no pods were seen and deployment wasn't scaled up until manual intervention ? There is another Model Endpoint that in the stale stale (deployment replicas 0) in case we need to investigate.

The manual scalup eventually failed with this error:

```
INFO 2025-11-07 17:36:38.264 build_utils.py:179] Detected model architecture: LlamaForCausalLM
566it [00:32, 17.54it/s]
566it [00:32, 17.43it/s]
INFO 2025-11-07 17:37:10.695 build_utils.py:239] Total time of reading and converting rank1 checkpoint: 32.63 s
INFO 2025-11-07 17:37:10.695 build_utils.py:239] Total time of reading and converting rank0 checkpoint: 32.43 s
[11/07/2025-17:42:27] [TRT] [E] [resizingAllocator.cpp::allocate::78] Error Code 1: Cuda Runtime (out of memory)
[11/07/2025-17:42:29] [TRT] [E] [resizingAllocator.cpp::allocate::78] Error Code 1: Cuda Runtime (out of memory)
[11/07/2025-17:42:32] [TRT] [E] [myelinResourceManager.cpp::allocate::196] Error Code 2: OutOfMemory (Requested size was 71603093760 bytes.)
Traceback (most recent call last):
```

Here is the describe node log where the pods getting scheduled:

```
Name:               ip-10-10-3-26.ec2.internal
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/instance-type=g6e.12xlarge
                    beta.kubernetes.io/os=linux
                    cml.cdp.cloudera.com/instanceGroupName=mlgpu-b6ea
                    cml.cloudera.com/idempotency-token=c37d7e67-f19a-4bec-a57d-44ada5aaec52
                    failure-domain.beta.kubernetes.io/region=us-east-1
                    failure-domain.beta.kubernetes.io/zone=us-east-1d
                    k8s.io/cloud-provider-aws=c77f9a586e930d5f04bf451d279839cf
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=ip-10-10-3-26.ec2.internal
                    kubernetes.io/os=linux
                    liftie.cloudera.com/instance-group-id=ig-prz9mkc9
                    metering.cdp.cloudera.com/isMetered=true
                    metering.cdp.cloudera.com/resourceIdFromCrn=c2a712d0-cfd8-493b-90f5-9779453e6fef
                    metering.cdp.cloudera.com/resourceTypeFromCrn=mlserving
                    metering.cdp.cloudera.com/serviceType=AI_INFERENCE
                    metering.cdp.cloudera.com/serviceTypeFromCrn=ml
                    node.kubernetes.io/instance-type=g6e.12xlarge
                    role.node.cloudera.com/gpu-details=L40S-45776
                    role.node.kubernetes.io/gpu=true
                    topology.ebs.csi.aws.com/zone=us-east-1d
                    topology.k8s.aws/zone-id=use1-az1
                    topology.kubernetes.io/region=us-east-1
                    topology.kubernetes.io/zone=us-east-1d
Annotations:        alpha.kubernetes.io/provided-node-ip: 10.10.3.26
                    csi.volume.kubernetes.io/nodeid:
                      {"ebs.csi.aws.com":"i-07beb49a334032341","efs.csi.aws.com":"i-07beb49a334032341","s3.csi.aws.com":"ip-10-10-3-26.ec2.internal"}
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 10.10.3.26/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 100.100.255.192
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 11 Oct 2025 01:14:29 +0100
Taints:             nvidia.com/gpu=true:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  ip-10-10-3-26.ec2.internal
  AcquireTime:     <unset>
  RenewTime:       Fri, 07 Nov 2025 17:49:02 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Sat, 11 Oct 2025 01:15:08 +0100   Sat, 11 Oct 2025 01:15:08 +0100   CalicoIsUp                   Calico is running on this node
  MemoryPressure       False   Fri, 07 Nov 2025 17:46:26 +0000   Sat, 11 Oct 2025 01:14:29 +0100   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Fri, 07 Nov 2025 17:46:26 +0000   Sat, 11 Oct 2025 01:14:29 +0100   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Fri, 07 Nov 2025 17:46:26 +0000   Sat, 11 Oct 2025 01:14:29 +0100   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Fri, 07 Nov 2025 17:46:26 +0000   Sat, 11 Oct 2025 01:15:00 +0100   KubeletReady                 kubelet is posting ready status
Addresses:
  InternalIP:   10.10.3.26
  ExternalIP:   3.238.148.205
  InternalDNS:  ip-10-10-3-26.ec2.internal
  Hostname:     ip-10-10-3-26.ec2.internal
  ExternalDNS:  ec2-3-238-148-205.compute-1.amazonaws.com
Capacity:
  cpu:                48
  ephemeral-storage:  536793068Ki
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             390834728Ki
  nvidia.com/gpu:     4
  pods:               110
Allocatable:
  cpu:                47625m
  ephemeral-storage:  492561007002
  hugepages-1Gi:      0
  hugepages-2Mi:      0
  memory:             389057064Ki
  nvidia.com/gpu:     4
  pods:               110
System Info:
  Machine ID:                 ec2aaadfb40af892f2d62fe93bb8afcd
  System UUID:                ec2aaadf-b40a-f892-f2d6-2fe93bb8afcd
  Boot ID:                    19c03b9e-739a-40e2-82ee-79936722fff1
  Kernel Version:             6.1.153-175.280.amzn2023.x86_64
  OS Image:                   Amazon Linux 2023.9.20250929
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  containerd://1.7.27
  Kubelet Version:            v1.32.9-eks-113cf36
  Kube-Proxy Version:         v1.32.9-eks-113cf36
ProviderID:                   aws:///us-east-1d/i-07beb49a334032341
Non-terminated Pods:          (12 in total)
  Namespace                   Name                                                               CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                                                               ------------  ----------  ---------------  -------------  ---
  istio-system                istio-cni-node-zjqjj                                               100m (0%)     0 (0%)      100Mi (0%)       0 (0%)         27d
  istio-system                ztunnel-xsjjw                                                      200m (0%)     0 (0%)      512Mi (0%)       0 (0%)         27d
  kube-system                 calico-node-wwv5r                                                  250m (0%)     0 (0%)      0 (0%)           0 (0%)         27d
  kube-system                 ebs-csi-node-d9lgf                                                 70m (0%)      0 (0%)      192Mi (0%)       0 (0%)         27d
  kube-system                 efs-csi-node-n4tlv                                                 260m (0%)     0 (0%)      384Mi (0%)       0 (0%)         27d
  kube-system                 kube-proxy-vrstr                                                   100m (0%)     0 (0%)      0 (0%)           0 (0%)         27d
  kube-system                 nvidia-device-plugin-ldwvp                                         0 (0%)        0 (0%)      0 (0%)           0 (0%)         27d
  kube-system                 s3-csi-node-t2q48                                                  30m (0%)      0 (0%)      120Mi (0%)       768Mi (0%)     27d
  logging                     cdp-fluentd-98jbd                                                  0 (0%)        0 (0%)      400Mi (0%)       700Mi (0%)     27d
  monitoring                  dcgm-exporter-x4jm6                                                100m (0%)     100m (0%)   600Mi (0%)       600Mi (0%)     27d
  monitoring                  monitoring-prometheus-node-exporter-qxhwr                          100m (0%)     200m (0%)   30Mi (0%)        50Mi (0%)      27d
  serving-default             llama-endpoint-paul-predictor-00001-deployment-767c99487b-7tz6b    22125m (46%)  2 (4%)      177280Mi (46%)   1Gi (0%)       45m
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests        Limits
  --------           --------        ------
  cpu                23335m (48%)    2300m (4%)
  memory             179618Mi (47%)  3142Mi (0%)
  ephemeral-storage  0 (0%)          0 (0%)
  hugepages-1Gi      0 (0%)          0 (0%)
  hugepages-2Mi      0 (0%)          0 (0%)
  nvidia.com/gpu     2               2
Events:              <none>
```

Deployment has scaled down

```
❯ k get deployment/llama-endpoint-paul-predictor-00002-deployment -n serving-default
NAME                                             READY   UP-TO-DATE   AVAILABLE   AGE
llama-endpoint-paul-predictor-00002-deployment   0/0     0            0           85m
```

This is a bad profile, and should have been removed.


### Model Download hangs

```
kubectl  logs -f model-registry-v2-65bc7846f8-p6kzh -c model-registry-v2 -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```

```
kubectl  logs -f model-registry-v2-65bc7846f8-6bbzb -c model-registry-v2 -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```

```
kubectl delete pod model-registry-v2-65bc7846f8-p6kzh -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```

```
kubectl delete pod model-registry-v2-65bc7846f8-6bbzb -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```

Get logs from namespace:

```
kubectl get pods -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```

Kill:

```
kubectl delete pod model-registry-v2-65bc7846f8-vs5q2  model-registry-v2-65bc7846f8-wjtmj -n mlx --kubeconfig=model-registry-ml-66ae42c5-565-kubeconfig.yaml
```
