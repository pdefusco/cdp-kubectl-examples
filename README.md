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
