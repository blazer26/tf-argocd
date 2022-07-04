# Build a KIND Kubernetes Cluster with Terraform and Argo CD for testing purposes

This project it's demonstrating how to use [Terraform](https://www.terraform.io/) together with [Argo CD](https://argo-cd.readthedocs.io/en/stable/) to create and manage the Kubernetes cluster on [Kind](https://kind.sigs.k8s.io/).

## Prerequisites

For the sake of consistency please take into account using these versions:

1. Terraform CLI installed ( Terraform v1.1.4 )
2. Docker or containerd ( containerd.io-1.2.13 )
3. Kind ( version 0.14.0 )
4. Kubectl ( v1.24.2 )


## Getting Started

### Terraform

First, clone that repo:
```shell
$ git clone https://github.com/blazer26/tf-argocd.git
$ cd tf-argocd
```

Then initialize Terraform config:
```shell
$ terraform init
```

Review the actions plan:
```shell
$ terraform plan
```

Run the Terraform actions:
```shell
$ terraform apply
```

We should have our cluster ready and running. Let’s display a list of Kind clusters:
```shell
$ kind get clusters
cluster-1
```

Let's also display the list of the nodes created:
```shell
$ kubectl get nodes -o wide
NAME                      STATUS   ROLES                  AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE       KERNEL-VERSION           CONTAINER-RUNTIME
cluster-1-control-plane   Ready    control-plane,master   19h   v1.23.4   172.18.0.4    <none>        Ubuntu 21.10   5.17.6-200.fc35.x86_64   containerd://1.5.10
cluster-1-worker          Ready    <none>                 19h   v1.23.4   172.18.0.3    <none>        Ubuntu 21.10   5.17.6-200.fc35.x86_64   containerd://1.5.10
cluster-1-worker2         Ready    <none>                 19h   v1.23.4   172.18.0.2    <none>        Ubuntu 21.10   5.17.6-200.fc35.x86_64   containerd://1.5.10
cluster-1-worker3         Ready    <none>                 19h   v1.23.4   172.18.0.5    <none>        Ubuntu 21.10   5.17.6-200.fc35.x86_64   containerd://1.5.10
```
### Argo CD

Now we can go to the Argo CD web console. To access it easily on the local port let’s enable port-forward:

```shell
kubectl port-forward service/argocd-server 8443:443 -n argocd
```
Now, we can display the Argo CD web console on the https://localhost:8443. The default username is admin.
The password is auto-generated by the Argo CD. We can find it inside the Kubernetes Secret argocd-initial-admin-secret.

```shell
kubectl get secret argocd-initial-admin-secret -n argocd --template={{.data.password}} | base64 -d
```
Here’s we can see the list of our Argo CD Applications. The cluster-config has the auto-sync option enabled. For the demo purposes, we left there a manual approval for the other apps ( grafana, nginx & prometheus ).

### Nginx

To access the site "http://local.ecosia.org/tree" we need to add in our local host file "/etc/hosts"
```shell
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 local.ecosia.org
```
### Grafana

To access Grafana we need to enable port-forward:
```shell
$ kubectl port-forward service/grafana 3000:3000 -n monitoring
```

## Results

After running the previous command you receive:
* 3-nodes Kind cluster running locally
* Argo CD installed on Kind
* Nginx ready to use with Ingress
* Prometheus & Grafana for monitoring
