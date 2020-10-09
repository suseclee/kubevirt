
# Deploy KubeVirt Operator
I used SUSE kubevirt mages: registry.suse.de/devel/virt/sle-15-sp2/containers/suse/sles/15.2/virt-operator:0.33.0
```
kubectl apply -f kubevirt-operator.yaml
```
### Add privillaged in kubevirt  
```
kubectl -n kubevirt create rolebinding kubvirt-privileged-rolebinding --clusterrole=suse:caasp:psp:privileged --group=system:serviceaccounts:kubevirt
```
### or
```
kubectl apply -f grant-kubevirt-privileges.yaml
```
### Create kubevirt cr
```
kubectl create -f kubevirt-cr.yaml
kubectl get all -n kubevirt
```
# Deploy CDI (Containerizeed data import)
There is no suse images yet for cdi-operator, cdi-controller, cdi-importer, cdi-cloner, cdi-apiserver, cdi-uploadserver, and cdi-uploadproxy.
```
kubectl create -f cdi-operator.yaml
kubectl create -f cdi-cr.yaml

kubectl get all -n cdi
```

