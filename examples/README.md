### In order to upload data to your cluster, the cdi-uploadproxy service must be accessible from outside the cluster.
```
kubectl apply -f cdi-uploadproxy.yaml
```
Check NodePort for cdi-uploadproxy-nodeport 
```
kubectl get svc -n cdi
```
### Upload local image to PVC
Worker Node IPs: 10.17.0.227, 10.17.1.109, 10.17.2.40
```
kubectl virt image-upload --pvc-name=sles15sp2-local --image-path=/home/chang/go/src/github.com/SUSE/skuba/ci/infra/libvirt/SLES15-SP2-JeOS.x86_64-15.2-OpenStack-Cloud-Build15.167.qcow2 --pvc-size=4Gi --uploadproxy-url=https://10.17.0.227:31001 --insecure
```

### Deploy sles15sp2 VMI
```
kubectl apply -f vm_sles15sp2_localPVC_userData.yaml

kubectl get vmi --watch
```
### Access to VM
```
kubectl virt console sles15sp2-vm-local
kubectl virt vnc sles15sp2-vm-local
```
### Access through SSH
```
kubectl virt expose vmi sles15sp2-vm-local --name=sles15sp2-ssh --port=20222 --target-port=22 --type=NodePort

kubectl get svc

# Worker Node IPs: 10.17.0.227, 10.17.1.109, 10.17.2.40
ssh sles@10.17.0.227 -p <NodePort>
```

# Installing Kubevirt client (virtctl/virt plugin)
### Option 1 virtctl 
##### SUSE Internal
```
sudo zypper ar http://download.suse.de/ibs/Devel:/Virt:/SLE-15-SP2/SUSE_SLE-15-SP2_GA_standard/ virt-sles15sp2-GA
sudo zypper ar http://download.suse.de/ibs/Devel:/Virt:/SLE-15-SP2/SUSE_SLE-15-SP2_Update_standard/ virt-sles15sp2-update
sudo zypper in kubevirt-virtctl
```
##### Upstream
```
export VERSION=v0.26.1
wget https://github.com/kubevirt/kubevirt/releases/download/${VERSION}/virtctl-${VERSION}-linux-x86_64

mv virtctl-${VERSION}-linux-x86_64 virtctl
chmod +x virtctl 
```

### Option 2 kubectl virt plugin
```
curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/krew.tar.gz"
tar zxvf krew.tar.gz
./krew-linux_amd64 install krew
# Add $HOME/.krew/bin directory to your PATH environment variable. 
vi ~/.bashrc
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"

kubectl krew search

# Keep your plugins up-to-date:
kubectl krew upgrade

kubectl krew install virt
kubectl krew uninstall virt
```
