# If installed with kubeadm
kubectl -n kube-system get configmap kubeadm-config -o yaml | grep clusterName

kubectl cluster-info
