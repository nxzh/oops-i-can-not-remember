# Create Service with file

## What is a Service?

Expose internal service to outside. 
Service can selete a group of Pods by label. Outside uses Service to visit the Pods.
So that Pods in the Kubernetes cluster can stop or change IP without influence outside.

## Service Configuration file

### First Create Deployments with labels

At first, we need to create Deployment Configuration files with __labels__, httpd.

        apiVersion: apps/v1beta1
        kind: Deployment
        metadata:
        name: httpd
        spec:
        replicas: 3
        template:
            metadata:
            labels:
                run: httpd
            spec:
            containers:
            - name: httpd
                image: httpd
                ports:
                - containerPort: 80

### Then Create Service Configuration files

        apiVersion: v1
        kind: Service
        metadata: 
        name: httpd-svc
        spec:
        selector:
            run: httpd
        ports:
        - protocol: TCP
            port: 8080
            targetPort: 80

* `selector`: will choose pods which label is httpd.
* `port` and `targetPort`: will map pods' 80 port to Service's 8080 port. So we can visit service's 8080 port


        [vagrant@k8s-master ~]$ kubectl get pods  -o wide
        NAME                    READY   STATUS    RESTARTS   AGE   IP            NODE        NOMINATED NODE   READINESS GATES
        curl-66959f6557-h2rzn   1/1     Running   6          2d    10.244.1.8    k8s-node1   <none>           <none>
        httpd-8c6c4bd9b-b5ncx   1/1     Running   0          98m   10.244.2.2    k8s-node2   <none>           <none>
        httpd-8c6c4bd9b-qz2gj   1/1     Running   0          98m   10.244.1.11   k8s-node1   <none>           <none>
        httpd-8c6c4bd9b-w4xgx   1/1     Running   0          98m   10.244.2.3    k8s-node2   <none>           <none>

### Access Service

    curl -k <IP>:<PORT>

or, can visit __inside PODS__ by domain name:

    kubectl run curl --image=radial/busyboxplus:curl -i --tty

or if already ran this command before, use the following:

    kubectl attach curl-xxxxxxxxx-xxxxx -c curl -i -t

After login to the POD:

    [ root@curl-87b54756-vsf2s:/ ]$ curl -k httpd-svc.default:8080

Means you're visiting the httpd-svc service which is in the `default` namespace.

### External network visiting Service

1. Cluster IP: default, can only be visited from Kubernetes Cluster IP and Pod.
2. NodePort: External(not in Cluster) network can visit by <NodeIP>:<NodePort>
3. LoadBalancer: Service use cloud provider's load balancer to provide the external visiting. Cloud Provide will responsible for direct the traffic to Service.

Example:

#### NodePort

        apiVersion:v1
        kind: Service
        metadata:
        name: httpd-svc
        spec:
        type: NodePort
        selector:
            run: httpd
        ports:
        - protocol: TCP
            port: 8080
            targetPort:80

Kubernetes will choose a random port from range 30000-32767 to expose in each __Node__.

        [vagrant@k8s-master ~]$ kubectl get service httpd-svc
        NAME        TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
        httpd-svc   NodePort   10.108.170.164   <none>        8080:31452/TCP   25s

So we can use port `31452` to visit each node:

        curl -k 192.168.50.101:31452

