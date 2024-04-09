
Reference doc: https://spacelift.io/blog/kubernetes-ingress

## Nginx ingress controller setup and Azure DNS setup

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

```
kubectl get service ingress-nginx-controller --namespace=ingress-nginx
NAME                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx-controller   LoadBalancer   10.0.###.###   172.###.###.###   80:32240/TCP,443:30240/TCP   156m
root@practice-9-apr-24-li-vm:~#
```

Browsing to this EXTERNAL-IP address will show you the NGINX 404 page. This is because we have not set up any routing rules for our services yet.

### Azure DNS setup

Get the DNS from GoDaddy or freenom or any vendor

Go to https://portal.azure.com/ >> DNS zones >> Create DNS zone with DNS name

https://learn.microsoft.com/en-us/azure/dns/dns-getstarted-portal
https://learn.microsoft.com/en-us/azure/dns/dns-delegate-domain-azure-dns

AFter the DNS Zone created, name servers should be configured at DNS settings.

After that, try this command: nslookup -type=SOA dnsname.com

You should get similar output mentioend below - this will take 20 to 30 mins to be available.

```
OUTPUT:
nslookup -type=SOA <dnsname.com>
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
<dnsname.com>
        origin = ns1-31.azure-dns.com
        mail addr = azuredns-hostmaster.microsoft.com
        serial = 1
        refresh = 3600
        retry = 300
        expire = 2419200
        minimum = 300

```

Create a new record set with submit domain name and configure ingress nginx controller EXTERNAL-IP address
ex: create record set >> nexus 

![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/5de74209-17d8-4eb5-a224-380fa3df71d5)

Or if there are many apps and services will be deployed to AKS cluster, configure `*.dnsname.com`, see below example

![image](https://github.com/DevOpsPlatform/DevOps-2024/assets/24622526/cb81ba38-cfe2-4144-978f-e149c79be416)


Sample Deployments: https://github.com/DevOpsPlatform/DevOps-2024/tree/k8s/ymlfiles/ingress 

**After Practice delete all resources** : `az group delete --resource-group MyResourceGroup`
