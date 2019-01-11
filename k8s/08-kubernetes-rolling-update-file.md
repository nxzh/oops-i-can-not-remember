# Rolling Update

## How to Rolling Update

Assuming now old version is deployed:

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
            image: httpd:2.2.31
            ports: 
            - containerPort: 80

run `kubectl apply -f httpd.yml` to deploy.

    [vagrant@k8s-master rolling-update]$ kubectl get deployment -o wide
    NAME    READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES         SELECTOR
    httpd   3/3     3            3           117s   httpd        httpd:2.2.31   run=httpd

    [vagrant@k8s-master rolling-update]$ kubectl get replicaset
    NAME               DESIRED   CURRENT   READY   AGE
    httpd-76cfb94bf4   3         3         3       3m58s

Then, try to upgrade the version by updating the `httpd.yml`.
    
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
            image: httpd:2.2.32
            ports: 
            - containerPort: 80

Then run `kubectl apply -f httpd.yml`:

    [vagrant@k8s-master rolling-update]$ kubectl get replicaset
    NAME               DESIRED   CURRENT   READY   AGE
    httpd-6cf6bf9f57   3         3         3       49s
    httpd-76cfb94bf4   0         0         0       20m

## How to Roll Back?

Record the deployment when deploying by adding the `--record`:

    kubectl apply -f httpd.v1.yml --record

To review the record history:

    kubectl rollout history deployment httpd

To roll back:

    kubectl rollout undo deployment httpd --to-revision=1