 # Connect to Azure Kubernetes

Download and install azure command line interface (AzureCLI) from: https://aka.ms/installazurecliwindows 

Extra info: https://docs.microsoft.com/nl-nl/cli/azure/install-azure-cli-windows?tabs=azure-cli

Install az aks by running from command line: 
```
> az aks install-cli 
```

Add kubectl to path trought command line (with administrator rights):
```
> setx -m PATH "%PATH%;C:\Users\<username>\.azure-kubelogin" 
> setx -m PATH "%PATH%;C:\Users\<username>\.azure-kubectl" 
```

Test if kubectl works:
```
> kubectl
```

Login to azure:
```
> az login
> az account set --subscription "[subscription]"
```

Connect kubectl to the correct cluster:
```
> az aks get-credentials --resource-group [resourcegroup] --name [kubernetesname]
```

Test if kubectl is connected:
```
> kubectl top nodes
```

# Cluster management

View the available clusters that are merged in the current config:
```
> kubectl config view
```

Switch to cluster:
```
> kubectl config use-context UltimoProductionCloud
```

# Node management

List nodes:
```
> kubectl get nodes

NAME                             STATUS   ROLES   AGE   VERSION
aks-nplin1-92779247-vmss000000   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000001   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000002   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000003   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000004   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000005   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000006   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000007   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000008   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss000009   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss00000a   Ready    agent   67d   v1.20.7
aks-nplin1-92779247-vmss00000c   Ready    agent   62d   v1.20.7
aks-nplin1-92779247-vmss00000d   Ready    agent   55d   v1.20.7
aksnpwin1000007                  Ready    agent   48d   v1.20.7
aksnpwin1000008                  Ready    agent   48d   v1.20.7
aksnpwin100000a                  Ready    agent   67d   v1.20.7
aksnpwin100000b                  Ready    agent   67d   v1.20.7
```

List node usage:
```
> kubectl top nodes

NAME                             CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%   
aks-nplin1-92779247-vmss000000   356m         4%     35383Mi         70%
aks-nplin1-92779247-vmss000001   423m         5%     45339Mi         89%
aks-nplin1-92779247-vmss000002   375m         4%     42377Mi         84%
aks-nplin1-92779247-vmss000003   396m         5%     41111Mi         81%
aks-nplin1-92779247-vmss000004   379m         4%     43356Mi         86%
aks-nplin1-92779247-vmss000005   450m         5%     42777Mi         84%
aks-nplin1-92779247-vmss000006   520m         6%     41485Mi         82%
aks-nplin1-92779247-vmss000007   485m         6%     40924Mi         81%
aks-nplin1-92779247-vmss000008   564m         7%     43703Mi         86%
aks-nplin1-92779247-vmss000009   431m         5%     38742Mi         76%       
aks-nplin1-92779247-vmss00000a   726m         9%     44567Mi         88%
aks-nplin1-92779247-vmss00000c   250m         3%     37883Mi         75%
aks-nplin1-92779247-vmss00000d   530m         6%     47257Mi         93%
aksnpwin1000007                  408m         5%     6163Mi          12%
aksnpwin1000008                  256m         3%     3345Mi          6%
aksnpwin100000a                  424m         5%     5968Mi          11%
aksnpwin100000b                  440m         5%     5807Mi          11%
```

Mark node as unschedulable, to prevent new pods from being deployed on this node:
```
> kubectl cordon [nodename]
```

Mark node as schedulable again:
```
> kubectl uncordon [nodename]
```

Drain node (this will cause all pods to be rescheduled to another node):
```
> kubectl drain --ignore-daemonsets=true [nodename]
```

Delete node from nodepool:
```
> kubectl delete node [nodename]
```

List pods running on a node:
```
> kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=[nodename]
```

# Deployment management

List all deployments:
```
> kubectl get deployments --all-namespaces
```

Restart deployment:
```
> kubectl restart deployment [deployment] -n [namespace]
```

Delete deployment:
```
> kubectl delete deployment [deployment] -n [namespace]
```

# Pod management

List all pods:
```
> kubectl get pods --all-namespaces
```

List pods in namespace:
```
> kubectl get pods -n [namespace]
```

List pod details:
```
> kubectl describe pods [pod] -n [namespace]
```

Delete pod:
```
> kubectl delete pods [pod] -n [namespace]
```

View log:
```
> kubectl logs [pod] -n [namespace]
```

# Debugging Linux Containers

Open interactive bash:
```
> kubectl exec [pod] -n [namespace] --stdin --tty -- /bin/bash
```

Copy file from pod to local:
```
> kubectl cp [namespace]/[pod]:tmp/foo /tmp/bar
```

Copy local file to pod:
```
> kubectl cp /tmp/bar [namespace]/[pod]:tmp/foo
```

# Debugging Windows Containers

Open interactive command shell:
```
> kubectl exec [pod] -n [namespace] -i -- cmd
```

Create dump from .Net IIS application:
```
List application pools
> %systemroot%\system32\inetsrv\AppCmd.exe list apppool

Start application pools
> %systemroot%\system32\inetsrv\AppCmd.exe start apppool /apppool.name:"DefaultAppPool" 

Start powershell
> powershell

Dowload procdump
> Invoke-WebRequest -Uri  https://download.sysinternals.com/files/Procdump.zip  -OutFile C:\procdump.zip
> Expand-Archive "C:\procdump.zip" "C:\" 

Create dump for w3wp.exe (the option -w waits until the process is terminated/crashed) 
> procdump64 -t -e -n 1000 -ma -w -g w3wp.exe
```

Copy file from pod to local:
```
> kubectl cp [namespace]/[pod]:c:\procdump.zip procdump.zip
```

# Troublesshoot nginx ingress controller

Download ngingx controller config
```
> kubectl exec -it -n [ngingxnamespace] [nginxpod] -- cat /etc/nginx/nginx.conf 
```