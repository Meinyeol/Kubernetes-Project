# proyecto_cda
## Preparation
### Out of GCP
#### Running Vagrant box
```
vagrant init dgzlopes/cda-project \
  --box-version 0.1
vagrant up
```
#### Configure gcloud
```
gcloud init
```

#### Install Powerfulseal
```
pip install powerfulseal

```
### GCP Shell
```
pip3 install powerfulseal --user
PATH=$PATH:/home/danielgonzalezlopes/.local/bin
gcloud compute config-ssh
gcloud config set project marine-foundry-184612
gcloud config set compute/zone us-central1-a
gcloud config set compute/region us-central1-a
```

### Boostrap GKE Kubernetes Cluster
- 3 node cluster (Default config! Using Web UI)
- Point local Kubectl to K8s cluster (Using GCP Web UI command)

### Deploy Microservices demo 
```
cd microservices-demo
kubectl create namespace sock-shop
kubectl apply -f complete-demo.yaml
```
### Deploy Weave Scope
```
# We are using GKE! So we have to get clusteradmin
kubectl create clusterrolebinding "cluster-admin-$(whoami)" --clusterrole=cluster-admin --user="$(gcloud config get-value core/account)"
```
```
kubectl apply -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```
```
# Open in your browser
kubectl port-forward -n weave "$(kubectl get -n weave pod --selector=weave-scope-component=app -o jsonpath='{.items..metadata.name}')" 4040
```
### Deploy Prometheus/Graphana
```
kubectl create namespace monitoring
kubectl create -f ./deploy/kubernetes/manifests-monitoring
```
### Simulate load
```
docker run --net=host weaveworksdemos/load-test -h [frontendIp:port] -r 100 -c 2
```





--------
VagrantboxTemplate
```
sudo apt-get update
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev python-openssl git curl
curl https://pyenv.run | bash
export PATH="/home/vagrant/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
exec $SHELL
pyenv install 2.7.15
pyenv install 3.7.0
pyenv shell 2.7.15 3.7.0

export CLOUD_SDK_REPO="cloud-sdk-$(lsb_release -c -s)"
echo "deb http://packages.cloud.google.com/apt $CLOUD_SDK_REPO main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
sudo apt-get update && sudo apt-get install -y google-cloud-sdk

sudo apt-get install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker

sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

