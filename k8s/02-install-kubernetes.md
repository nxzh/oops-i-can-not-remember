# Install Kubernets On CentOS 7

## Objects

1. master   (192.168.50.100)
2. node1    (192.168.50.101)
3. node2    (192.168.50.102)
4. nfs      (192.168.50.99)

## Preparation (For all machines)

### Close SELINUX

        setenforce 0
        sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux

### Disable Swap

1. Close by command:

        swapoff -a

2. Ensure it will not re-enable after reboot:
        
        vi /etc/fstab
        # /dev/mapper/centos-swap swap swap defaults 0 0

### Enable br_netfilter

        modprobe br_netfilter
        echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables

### Install Docker-CE (Skip if vagrant has already installed)

        yum install -y yum-utils device-mapper-persistent-data lvm2
        yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
        yum install -y docker-ce

## Install Kubernetes

### Install Kubernetes package (all machines)

        vi /etc/yum.repos.d/kubernetes.repo

Then insert:

[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

Then save and quit. Run:

        yum install -y kubelet kubeadm kubectl


### Change Cgroup (all machines)

Check docker's group:

    docker info | grep -i cgroup

Add Kubernetes too the group:

    sed -i 's/cgroup-driver=systemd/cgroup-driver=cgroupfs/g' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

Reload systemd:

    systemctl daemon-reload
    systemctl restart kubelet
    systemctl enable kubelet.service

### Initialize the Kubernetes cluster (on master)

    kubeadm init --apiserver-advertise-address=192.168.50.100 --pod-network-cidr=10.244.0.0/16


### Config kubectl (on master)

        mkdir -p $HOME/.kube
        sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
        sudo chown $(id -u):$(id -g) $HOME/.kube/config

Auto complete:

        echo "source <(kubectl completion bash)" >> ~/.bashrc 


### Install Pod network (on master)

        kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

### Add node1 and node2 (on node1 and node2)

        kubeadmin join --token <TOKEN> --discovery-token-ca-cert-hash <DISCOVERY_TOKEN>


### Check Pods status (on master)

        kubectl get pods --all-namespaces

        NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE
        kube-system   coredns-86c58d9df4-7fk9m             1/1     Running   0          3m40s
        kube-system   coredns-86c58d9df4-dp2kb             1/1     Running   0          3m40s
        kube-system   etcd-k8s-master                      1/1     Running   0          3m
        kube-system   kube-apiserver-k8s-master            1/1     Running   0          2m59s
        kube-system   kube-controller-manager-k8s-master   1/1     Running   0          3m4s
        kube-system   kube-flannel-ds-amd64-k8264          1/1     Running   0          119s
        kube-system   kube-flannel-ds-amd64-vf5mk          1/1     Running   0          2m2s
        kube-system   kube-flannel-ds-amd64-zml7b          1/1     Running   0          2m35s
        kube-system   kube-proxy-ghx22                     1/1     Running   0          3m41s
        kube-system   kube-proxy-qznhg                     1/1     Running   0          119s
        kube-system   kube-proxy-t8jd7                     1/1     Running   0          2m2s
        kube-system   kube-scheduler-k8s-master            1/1     Running   0          3m6s

or display detail infor:

        kubectl get pods --all-namespaces -o wide 

        NAMESPACE     NAME                                 READY   STATUS    RESTARTS   AGE     IP           NODE         NOMINATED NODE   READINESS GATES
        kube-system   coredns-86c58d9df4-7fk9m             1/1     Running   0          11m     10.244.0.3   k8s-master   <none>           <none>
        kube-system   coredns-86c58d9df4-dp2kb             1/1     Running   0          11m     10.244.0.2   k8s-master   <none>           <none>
        kube-system   etcd-k8s-master                      1/1     Running   0          10m     10.0.2.15    k8s-master   <none>           <none>
        kube-system   kube-apiserver-k8s-master            1/1     Running   0          10m     10.0.2.15    k8s-master   <none>           <none>
        kube-system   kube-controller-manager-k8s-master   1/1     Running   0          10m     10.0.2.15    k8s-master   <none>           <none>
        kube-system   kube-flannel-ds-amd64-k8264          1/1     Running   0          9m28s   10.0.2.15    k8s-node2    <none>           <none>
        kube-system   kube-flannel-ds-amd64-vf5mk          1/1     Running   0          9m31s   10.0.2.15    k8s-node1    <none>           <none>
        kube-system   kube-flannel-ds-amd64-zml7b          1/1     Running   0          10m     10.0.2.15    k8s-master   <none>           <none>
        kube-system   kube-proxy-ghx22                     1/1     Running   0          11m     10.0.2.15    k8s-master   <none>           <none>
        kube-system   kube-proxy-qznhg                     1/1     Running   0          9m28s   10.0.2.15    k8s-node2    <none>           <none>
        kube-system   kube-proxy-t8jd7                     1/1     Running   0          9m31s   10.0.2.15    k8s-node1    <none>           <none>
        kube-system   kube-scheduler-k8s-master            1/1     Running   0          10m     10.0.2.15    k8s-master   <none>           <none>

## Summary

### Master running

1. kube-apiserver: API server
2. kube-scheduler: Schedular, decide what Pod will be running on what Node.
3. kube-controller-manager: Manager resources. For example: replication controller, endpoints controller, namespace controller, serviceaccounts controller.
4. etcd: Saving all configuration and resource status.
5. kube-flannel: Pod network 
6. kube-proxy: Allow appliaction to expose by Service
7. kubelet: The agent of node.

### Node running

1. kubelet
2. kube-proxy
