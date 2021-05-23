# Kubernetes-Scripts
### Scripts for bootstrapping Kubernetes on Debian and RHEL systems 

Run `docker-setup` first. If you want to use something like CRI-O or Podman, that is out of the scope of this document. 

After Kubernetes has been bootstrapped you need to run `kubeadm init` on the master node. After that, you'll receive some information no how to join other nodes. SWAP must be disabled as does SELinux. `sudo swapoff -va` will take care of it initially but you will need to remove the swap entry from `/etc/fstab` for persistence. Don't forget to add a network. I use Weave Net.

`kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"`

Discovery steps are as follows:

```
mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubeadm join <control-plane-host>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

Head back over to the master node and run `kubectl get nodes` 

You should see STATUS as Ready on all four, with the master being labeled with control-plane and master roles. 

To verify network discovery, etcd, and other services are running:

```
kubectl get pods -o wide --all-namespaces
```

Troubleshooting steps can be found here:

[Weave Net](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/#troubleshooting)

[Kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/)
