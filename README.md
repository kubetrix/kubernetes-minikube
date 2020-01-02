# kubernetes-minikube
Minikube setup with kubernetes

# Title
Minikube kubernetes setup guide using VirtualBox(Windows)

#### Prerequisites

- Gitbash software required for all below executions, Please download using below link

		https://gitforwindows.org/

	> Note:- Use Gitbash for all command execution
	
#### Setup VirtualBox
	
	https://www.virtualbox.org/wiki/Downloads

#### Download Minikube
 
	curl -L https://github.com/kubernetes/minikube/releases/download/v1.5.2/minikube-windows-amd64.exe --output minikube.exe
	
#### Download kubernetes (Use Gitbash for command execution)
	curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.16.0/bin/windows/amd64/kubectl.exe --output kubectl.exe

#### Minikube VM Setup with Kubernetes Cluster 

	minikube.exe start --vm-driver=virtualbox --bootstrapper=kubeadm --kubernetes-version=v1.16.0 --memory='4000mb'


| Options | Description |
| ------ | ----------- |
| --vm-driver |   Required for specifying driver type like virtualbox or sometime someone uses hypervisor. (In this  tutorial I am using virtualbox)|
| --bootstrapper |   Using for kubernetes kubeadm installation in minikube vm-driver|		
| --kubernetes-version |  Use Kubernetes version which you want, I have tested the same using v1.16.0|
| --memory |  Please specify the memory for minikube vm, default memory will be 2000mb|

TODO: 
> minikube.exe- Use the downloaded file for all minikube command execution

> **After execution wait for some time for VM to be up and running.**

#### Verify minikube installation using below command

	minikube.exe status
	
> Expected Output of above command

			host: Running
			kubelet: Running
			apiserver: Running
			kubeconfig: Configured

#### If you want to SSH in minikube VM then use below command 
	
	minikube.exe ssh
	
#### Add Kubectl config file to Environment Variable
	
Sometime kubectl.exe command not able to find config file (Kubernetes config file required for connecting to minikube cluster), It's better to configure the config file path using below process.
	
**Use below process for configuration**

	My-PC Properties->Advance System Settings-> Environment Variables(At bottom)-> Add new variable under User variable for user field, as mentioned below

	Variable 	- KUBECONFIG
	Value		- Kubectl context config file path (Default path- C:/Users/username/.kube/config)

#### Verify context added in file correctly for minikube 
	
	kubectl.exe config get-contexts
	
> It will show the current context(Current pointing cluster), i.e. minikube 
	
#### Verify kubernetes installation in minikube VM, run below command 
	
	kubectl.exe cluster-info
	
> This command will tell status for running kubernetes cluster.

## Sample app installation in minikube cluster 

#### Deploy Sample App
	kubectl.exe create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.10

| Options | Description |
| ------ | ----------- |
| create | Will create/apply new configuration in kubernetes |
| deployment | Please follow below mentioned link |
| hello-minikube | Deployment name you can provide whatever name you like |
| --image |	It will fetch the image which is present in gcr.io repo (Image starting name will specify the centralized repo name), If not present in local docker repo |	

> It will deploy below kubernetes resources

| Kubernetes Resource | Docs |
| ------ | ----------- |
| deployments | https://kubernetes.io/docs/concepts/workloads/controllers/deployment/ |
| Service |	https://kubernetes.io/docs/concepts/services-networking/service/ |
| ReplicaSet | https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/ |


#### Expose sample app deployment using NodePort
	
	kubectl.exe expose deployment hello-minikube --type=NodePort --port=8080 
		
Here we are eposing sample app to outside the cluster on node(VM) level.
| Options | Description |
| ------ | ----------- |
| expose | We usually use expose for exposing the services to outer world | 
| deployment | 	Kubernetes Resource, As mentioned Previously |
| hello-minikube | Previously used deployment name |
| --type | Using NodePort type, It will expose the deployment to node(VM Level) |  
| --port | exposed port no, So now this service is exposed on minikube VM port 8080 |

##### In kubernetes mainly we have 3 type of service

| ServiceTypes	 | Description | Docs Link |
| ------ | ----------- | --------- |
| NodePort | Expose deployment to node level | https://kubernetes.io/docs/concepts/services-networking/service/#nodeport | 
| ClusterIP | Expose deployment to cluster level | https://kubernetes.io/docs/concepts/services-networking/service/ |
| LoadBalancer | Expose deployment to LoadBalancer | https://kubernetes.io/docs/concepts/services-networking/service/#loadbalancer | 

#### Verify sample app status
	
* kubectl get pods (verify pod is running or not)
	
* kubectl get svc (verify svc is exposed on particualar port)
	
#### Access application 	
		
		minikube.exe service hello-minikube --url

> Run the exposed url in browser, you will be able to access the deployment.

### Setup minikube with RBAC(Secured)
	-	https://medium.com/@HoussemDellai/rbac-with-kubernetes-in-minikube-4deed658ea7b
	-  	https://docs.bitnami.com/kubernetes/how-to/configure-rbac-in-your-kubernetes-cluster/ (Used)

### Install helm 
	-	helm init --service-account tiller --override spec.selector.matchLabels.'name'='tiller',spec.selector.matchLabels.'app'='helm' --output yaml | sed 's@apiVersion: extensions/v1beta1@apiVersion: apps/v1@' | kubectl.exe apply -f -
	
	-- install helm v3.0.2 tiller is removed.
	-- helm repo add stable https://kubernetes-charts.storage.googleapis.com/ (Official repo)
	
#### UnInstall Minikube/Kubectl  
> Stop Minikube VM
	
	minikube.exe stop
		
> Delete Minikube VM configuration
		
	minikube.exe delete
		
> Delete below Folder Manually
		
		Minikube Folder - C:\users\{user}\.minikube
		
		Kubectl Folder - C:\users\{user}\.kube	
		
