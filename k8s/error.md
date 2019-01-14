---

Q:

    [root@k8s-master volume]# kubectl logs httpd-6cdc78c45c-2xtng
    Error from server: Get https://192.168.50.102:10250/containerLogs/default/httpd-6cdc78c45c-2xtng/httpd: Forbidden

A:

    [root@k8s-master volume]# sudo su
    [root@k8s-master volume]# vim /etc/kubernetes/manifests/kube-apiserver.yaml


search `no_proxy` and add node ip into it.