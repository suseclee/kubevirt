# In order to upload data to your cluster, the cdi-uploadproxy service must be accessible from outside the cluster.
kubectl apply -f cdi-uploadproxy.yaml

# Check NodePort for cdi-uploadproxy-nodeport 
kubectl get svc -n cdi

# Upload local image to PVC
# Worker Node IPs: 10.17.0.227, 10.17.1.109, 10.17.2.40
kubectl virt image-upload --pvc-name=sles15sp2-local --image-path=/home/chang/go/src/github.com/SUSE/skuba/ci/infra/libvirt/SLES15-SP2-JeOS.x86_64-15.2-OpenStack-Cloud-Build15.167.qcow2 --pvc-size=4Gi --uploadproxy-url=https://10.17.0.227:31001 --insecure


# Deploy sles15sp2 VMI
vi vm_sles15sp2_localPVC_userData.yaml
kubectl apply -f vm_sles15sp2_localPVC_userData.yaml

kubectl get vmi --watch

# Access to VM
kubectl virt console sles15sp2-vm-local
kubectl virt vnc sles15sp2-vm-local

# Access through SSH
kubectl virt expose vmi sles15sp2-vm-local --name=sles15sp2-ssh --port=20222 --target-port=22 --type=NodePort

kubectl get svc

# Worker Node IPs: 10.17.0.227, 10.17.1.109, 10.17.2.40
ssh sles@10.17.0.227 -p <NodePort>

