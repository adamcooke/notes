# Installing Rancher

### upgrade stuff

```
apt update
apt upgrade
reboot
```

### install k3s

```
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.24.10+k3s1" sh -s - server --cluster-init
```

### wait for node to be ready

```
kubectl get nodes
```

### copy config to root

```
sudo cp /etc/rancher/k3s/k3s.yaml /root/.kube/config
```

### download/install helm

```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

### add repos for rancher and cert-manager

```
helm repo add rancher-latest https://releases.rancher.com/server-charts/latest
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

### create namespace for rancher

```
kubectl create namespace cattle-system
```

### install cert-manager crds & then install from helm

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.7.1/cert-manager.crds.yaml
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.7.1
```

### install rancher

```
helm install rancher rancher-latest/rancher \
  --namespace cattle-system \
  --set hostname=rancher.infra.k.io \
  --set replicas=1 \
  --set bootstrapPassword=zZVpasnJUvcWWTHf \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=team@katapult.io \
  --set letsEncrypt.ingress.class=traefik
```
