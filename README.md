# DevOps-2024

**Installation of AWX**: https://ansible.readthedocs.io/projects/awx-operator/en/latest/installation/basic-install.html

**Requirements**: AKS cluster with size of each node is - 4cpu and 8gb RAM

**Install az cli and kubectl**: https://github.com/DevOpsPlatform/DevOps-2024/blob/k8s/1.Basics.md 

```
# cat awx.yml
# 4 cpu and 8 GB RAM nodepool
---
apiVersion: awx.ansible.com/v1beta1
kind: AWX
metadata:
  name: awx-demo
spec:
  service_type: loadbalancer
  auto_upgrade: false
```
--------------------------------------------
```
# cat kustomization.yml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  # Find the latest tag here: https://github.com/ansible/awx-operator/releases
  - github.com/ansible/awx-operator/config/default?ref=2.11.0
  - awx.yml
# Set the image tags to match the git version from above
images:
  - name: quay.io/ansible/awx-operator
    newTag: 2.11.0

# Specify a custom namespace in which to install AWX
namespace: awx
```
---------------------------------------------

https://github.com/DevOpsPlatform/DevOps-2024/tree/awx

```
git clone -b awx https://github.com/DevOpsPlatform/DevOps-2024

cd DevOps-2024

kubectl apply -k .

kubectl get pods -n awx

kubectl config set-context --current --namespace=awx

kubectl logs -f deployments/awx-operator-controller-manager -c awx-manager

kubectl get pods -l "app.kubernetes.io/managed-by=awx-operator"

kubectl get svc -l "app.kubernetes.io/managed-by=awx-operator"

kubectl get secret awx-demo-admin-password -o jsonpath="{.data.password}" | base64 --decode ; echos
```

After practice, delete all resources `az group delete --resource-group MyResourceGroup`
