# Servlerless Development Environment

Ever wanted to try out Serverless? This document will guide you through setting up [Nginx](https://nginx.org/en/)
forwarding to [Fission](https://github.com/fission/fission) installed with [Helm](https://github.com/kubernetes/helm)
and [Kubectl](https://kubernetes.io/docs/user-guide/kubectl/), on top of [Kubernetes](https://kubernetes.io)
through [Minikube](https://github.com/kubernetes/minikube), on top of native
[Docker in Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/).

I picked a machine with a recent Ubuntu OS on [DigitalOcean](https://www.digitalocean.com).

## Install Docker

To quote [Wikipedia](https://en.wikipedia.org/wiki/Docker_(software));
Docker uses the resource isolation features of the Linux kernel such as cgroups and kernel
namespaces, and a union-capable file system such as OverlayFS and others to allow independent "containers" to
run within a single Linux instance, avoiding the overhead of starting and maintaining virtual machines (VMs).

```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update
sudo apt-get install docker-ce
```

## Installing Kubectl

Kubectl is what I use to interact with Kubernetes. It's just a single binary.

```bash
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

## Installing Minikube

For this development machine, I've used Minikube to provide a Kubernetes implementation.
On Linux-based environments, you can use --vm-driver=none to use the host's native Docker installation.

```bash
sudo apt-get install socat
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.22.2/minikube-linux-amd64
chmod +x minikube
sudo mv minikube /usr/local/bin/

minikube start --vm-driver=none
```

## Installing Helm

Helm; The Kubernetes Package Manager. Installing Fission, which hosts serverless functions, is a oneliner with Helm.

```bash
curl -LO https://storage.googleapis.com/kubernetes-helm/helm-v2.6.1-linux-amd64.tar.gz
tar xzf helm-v2.6.1-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin

kubectl -n kube-system create sa tiller
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
helm init --service-account tiller
```
## Installing Fission

```bash
helm install --namespace fission --set serviceType=NodePort https://github.com/fission/fission/releases/download/v0.2.1/fission-all-v0.2.1.tgz
```

## Installing Nginx

After installing Fission, you can actually create functions from files and bind them to routes.

To make the Fission functions it publicly accessible without having to go through a Kubernetes Ingress setup, I use Nginx.

```bash
sudo apt-get install nginx
sudo ufw allow 'Nginx HTTP'
sudo cat >/etc/nginx/sites-enabled/default <<EOL
server {
        listen 80 default_server;
        listen [::]:80 default_server;
        location / {
                proxy_buffering off;
                # fission's router's NodePort is 31314
                proxy_pass http://localhost:31314;
        }
}
EOL
sudo service nginx reload
```

## Done

You can now use Fission, and do HTTP GET's on port 80 where Nginx is running.

So, create a Fission environment (Which defines a Docker base image, or more or less a VM template in which a function lives):

```bash
fission env create --name nodejs --image fission/node-env
```

Create the function:

```bash
curl -LO https://raw.githubusercontent.com/fission/fission/master/examples/nodejs/hello.js
fission function create --name hello --env nodejs --code hello.js
fission route create --method GET --url /hello --function hello
```

Call function:

```bash
curl http://localhost/hello
```
