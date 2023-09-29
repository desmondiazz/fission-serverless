## Getting started steps for running using fission on kubernetes

### Prerequisite (Linux)
 - minikube - https://minikube.sigs.k8s.io/docs/start/
 - kubectl - https://snapcraft.io/kubectl
 - helm - https://snapcraft.io/helm
 - fission - https://fission.io/docs/installation/#with-helm

### Intall fission on kubernetes cluster
```bash
export FISSION_NAMESPACE="fission"
kubectl create namespace $FISSION_NAMESPACE
kubectl create -k "github.com/fission/fission/crds/v1?ref=v1.19.0"
helm repo add fission-charts https://fission.github.io/fission-charts/
helm repo update
helm install --version v1.19.0 --namespace $FISSION_NAMESPACE fission \
  --set serviceType=NodePort,routerServiceType=NodePort \
  fission-charts/fission-all
```
### Install fission CLI

```bash
curl -Lo fission https://github.com/fission/fission/releases/download/v1.19.0/fission-v1.19.0-linux-amd64 \
    && chmod +x fission && sudo mv fission /usr/local/bin/
```
### Enable ingress controller on kubernetes
```bash
minikube addons enable ingress
```

### Check status of fission services
```bash
fission check
```
 ### Create NodeJs environment on fission
```bash
fission env create --name nodejs --image fission/node-env
```
### Create a fission function
```bash
fission function create --name hello-js --env nodejs --code hello.js
```
### Test the fission function
```bash
fission function test --name hello-js
```
### Create a route for the newly created fission function
```bash
fission ht create --name hello-js --url  /hello-js --method GET  --function hello-js --createingress=true
```
### Invoke the route
```bash
curl http://$(minikube ip)/hello-js
```