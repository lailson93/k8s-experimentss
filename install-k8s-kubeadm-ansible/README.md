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
- python 2.7
- Access passwordless
- TPC and UDP ports open between all hosts


### how to

First of all, you have to edit the config file present in the overlay you're gonna use. Normally you'll only have to change `ansible_user`, `docker_version` and `k8s_version`. 

For OS based on RedHat `k8s_version`: 1.19.10-0, `docker_version`: 19.03.15 and for Ubuntu's `k8s_version`: 1.19.10-00, `docker_version`: 5:19.03.15~3-0~ubuntu-

What you're gonna find in the config file is:

```
ansible_user: "ubuntu"

docker_version: "5:19.03.15~3-0~ubuntu-"
## 1.19.10-0 para centos
k8s_version: "1.19.10-00"
k8s_packages:
  - "kubectl"
  - "kubeadm"
  - "kubelet"

## master vars
k8s_network_cidr: "10.244.0.0/16"
k8s_prefliht_ignore: "all"
k8s_init_options: "--pod-network-cidr={{ k8s_network_cidr }} --ignore-preflight-errors={{ k8s_prefliht_ignore }}"
k8s_flannel_yml: "https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml"

### node vars
k8s_join_token: "--token={{ hostvars['K8S_TOKEN_HOLDER']['token'] }}"
k8s_join_hash: "--discovery-token-ca-cert-hash sha256:{{ hostvars['K8S_TOKEN_HOLDER']['hash'] }}"
master_internal_ip: "{{ hostvars['K8S_TOKEN_HOLDER']['ip'] }}"
k8s_join_options: "{{ k8s_join_token }} {{ k8s_join_hash }}"
```

Executing the playbook:

```sh
$ ansible-playbook -i overlays/stage/hosts site.yml --tags "setup" -e ansible_python_interpreter="/usr/bin/python2"
$ ansible-playbook -i overlays/stage/hosts site.yml --tags "tools" -e ansible_python_interpreter="/usr/bin/python2"
$ ansible-playbook -i overlays/stage/hosts site.yml --tags "k8smain" -e ansible_python_interpreter="/usr/bin/python2"
$ ansible-playbook -i overlays/stage/hosts site.yml --tags "addnode" -e ansible_python_interpreter="/usr/bin/python2"
```

### otptional to redhat 8

You have to specify the python path when running the ansible-playbook command as it follows:

```sh
$ ansible-playbook -i overlays/stage/hosts site.yml --tags "setup" -e ansible_python_interpreter="/usr/bin/python2"
```

```sh
$ sudo yum install python2
$ alias python=/usr/bin/python2
$ alias pip=/usr/bin/pip2

$ sudo /usr/bin/pip2 install selinux
$ sudo /usr/bin/pip2 install rpm ### optional
``` 

### troubleshoot

dns problems? try to restart coredns!

```sh
$ kubectl -n kube-system rollout restart deployment coredns
```

### references

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

https://kubernetes.io/docs/setup/production-environment/container-runtimes/

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/