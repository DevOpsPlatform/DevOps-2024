
Reference doc: https://spacelift.io/blog/kubernetes-ingress

#### Step 1 – Setup AKS cluster and connect

create AKS cluster manually on https://portal.azure.com/ 

**az cli installation on RHEL**: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=dnf
```
	sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

	sudo dnf install -y https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm

	sudo dnf install azure-cli -y
```

**az cli insall on ubuntu**: `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash`

**az cli install on windows**: https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli

**Install `kubectl` & `kubelogin`** : 

```
az aks install-cli

ln -s /usr/local/bin/kubectl /usr/bin/kubectl
ln -s /usr/local/bin/kubelogin /usr/bin/kubelogin
```

**Connect to cluster**:

```
az login

az aks get-credentials --resource-group practice-9-apr-24-rg --name practice-9-apr-24-aks
```

https://learn.microsoft.com/en-us/azure/aks/node-access

**Node details**:

	kubectl get nodes

	kubectl get nodes -o wide

#### Step 2 – Install the NGINX Ingress controller

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.3.0/deploy/static/provider/cloud/deploy.yaml`

**Output**:

![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/0f744936-32c1-4207-9254-30f8e9bdfff8)

Check the Ingress controller pod is running

`kubectl get pods --namespace ingress-nginx`

**Output**: 

![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/bd485691-cb9f-4369-bb0b-a422c1314461)

#### Step 3 – Check the NGINX Ingress controller has been assigned a public Ip address

`kubectl get service ingress-nginx-controller --namespace=ingress-nginx`

Browsing to this IP address will show you the NGINX 404 page. This is because we have not set up any routing rules for our services yet.


