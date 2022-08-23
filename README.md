# Setup on kd3 | Kubernetes
- Installation guide - [https://k3d.io/v5.4.1/](https://k3d.io/v5.4.1/)
## Create a cluster
```
k3d cluster create cloud-native --api-port 6550 -p "8080:80@loadbalancer" -p "8443:443@loadbalancer" --agents 2 --servers 3
k3d cluster list
export KUBECONFIG="$(k3d kubeconfig write cloud-native)"
kubectl cluster-info
```
## Setup Namespace
```
kubectl create namespace cloud
kubectl config set-context --current --namespace=cloud
```
## Modify Deployment
```
kubectl set image deployment web-server server=httpd
kubectl rollout status deployment web-server   
```
## HPA
```
kubectl autoscale deployment web-server --cpu-percent=50 --min=1 --max=10
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --namespace=cloud --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://web-server; done"
kubectl get hpa web-server --watch
kubectl get deployment web-server
```
# Reference
- https://github.com/codesenju/eks
- https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/