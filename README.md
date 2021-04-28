K8S PLAYBOOK
===

### INtro

k8s v1.19.10

You will install these packages on all of your machines, using `tools`'s tag from this playbook:
- kubeadm: the command to bootstrap the cluster.
- kubelet: the component that runs on all of the machines in your cluster and does things like starting pods and containers.
- kubectl: the command line util to talk to your cluster.

### Prerequisites

- Ansible 2.6.5
- Access passwordless


### how to

```
ansible-playbook -i overlays/stage/hosts site.yml --tags "setup"
ansible-playbook -i overlays/stage/hosts site.yml --tags "tools"
ansible-playbook -i overlays/stage/hosts site.yml --tags "k8smain"
ansible-playbook -i overlays/stage/hosts site.yml --tags "addnode"
```

### references

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
https://kubernetes.io/docs/setup/production-environment/container-runtimes/
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/