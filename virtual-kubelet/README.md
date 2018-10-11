# Virtual Kubelet #

[ACI](https://docs.microsoft.com/en-ca/azure/container-instances/) is a means to support the serverless model for containers on Azure.  It is the simplest approach to deploying containerized workloads on Azure.  It supports per second billing and does not require you to manage any infrastructure.  Given its burstable capabilities, it is ideal for supporting transient, on-demand workloads on Azure.

[Virtual kubelet](https://github.com/virtual-kubelet/virtual-kubelet) brings ACI support to Kubernetes.  Via virtual kubelet, ACI is represented as a virtual worker node on Kubernetes with (theoretically) infinite capacity. Virtual Kubelet is an open-source project led by Microsoft and essentially, it is a kubelet implementation that enables integration with external APIs. Today it supports integration with services such as ACI, Fargate, Azure IOT via pluggable "providers" and more is to come.  These providers expose the underlying container runtime to the virtual kubelet so that those containers can be made visible to the Kubernetes masters.

**Note, to run the example below, you need to have your AKS cluster deployed to a region where ACI is available.  As of now, that is "centralus,eastus,eastus2,westus,westus2,northeurope,westeurope,southeastasia,australiaeast"**

**Note, virtual kubelet support on AKS is very, very bleeding edge and should not be used in production.  As for now, the example below does not work because the kube-proxy being deployed to the virtual node and that is failing due to the volume type the kube-proxy pod requires is not supported on ACI.**

```sh
#To install virtual kubelet on the cluster helm is required.  See [README.md](../helm/README.md) for instructions on setting up helm on the cluster.

#After helm is initialized, install the connector
az aks install-connector --resource-group <resource group of your cluster> --name <name of your aks cluster> --connector-name virtual-kubelet --os-type Linux

#Verify that you can see the virtual node.
kubectl get nodes

NAME                                              STATUS    ROLES     AGE       VERSION
aks-nodepool1-16139398-0                          Ready     agent     10m       v1.11.2
aks-nodepool1-16139398-1                          Ready     agent     10m       v1.11.2
virtual-kubelet-virtual-kubelet-linux-centralus   Ready     agent     3m        v1.11.2

#Deploy the pod-details service to the virtual node
kubectl create -f pod-details-service.yaml
#You will see the pods scheduled to the virtual node
```

## Reference ##

* [AKS Virtual Kubelet](https://docs.microsoft.com/en-us/azure/aks/virtual-kubelet)

