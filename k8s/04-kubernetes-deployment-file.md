# Create Deployment with file

## Deployment Configuration file



Deployment Configuration file:

        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata: 
          name: nginx-deployment 
        spec: 
          replicas: 2
          template: 
            metadata:
              labels:
                app: web_server
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9


* apiVersion: Current configuration api version to 
* kind: The type of the resoure. For example, Deployment
* metadata: The metadata of the Resource.
    * name: name of the Resource. __MUST TO HAVE__

* spec: Specification of the Resource.
    
    * replicas: Number of the replicas. By default its value is __1__.
    * template: Define the POD
        * metadata: Define the metadata of the POD. At least define a label.
            * labels: label of the POD. key-value pair.
        * spec: Describe the specification of the POD.
            * containers: Describe the attributes of each container in the POD.
                * name: Name of the container.
                * image: Image of the container.

To let Kubernetes apply the configuration file, run:

        kubectl apply -f nginx.yml

To remove the dployments:

        kubectl delete -f nginx.yml

or 

        kubectl delete deployment nginx-deployment


## Modify Number of Replicas

Change `replicas` to 5. Then run:

        kubectl apply -f nginx.yml

        NAME                                READY   STATUS    RESTARTS   AGE    IP            NODE        NOMINATED NODE   READINESS GATES
        nginx-deployment-65998d8886-4mv6q   1/1     Running   0          3m5s   10.244.2.8    k8s-node2   <none>           <none>
        nginx-deployment-65998d8886-hq6r9   1/1     Running   0          25s    10.244.2.10   k8s-node2   <none>           <none>
        nginx-deployment-65998d8886-nc4mh   1/1     Running   0          25s    10.244.1.12   k8s-node1   <none>           <none>
        nginx-deployment-65998d8886-r7ps5   1/1     Running   0          25s    10.244.2.9    k8s-node2   <none>           <none>
        nginx-deployment-65998d8886-wv8df   1/1     Running   0          3m5s   10.244.1.11   k8s-node1   <none>           <none>

## Run Pods on specific node

Add new label for node:

        kubectl label node k8s-node1 disktype=ssd

Check label of the node:

        kubectl get node --show-labels

Modify the configuration file, add `nodeSelector`:

        apiVersion: extensions/v1beta1
        kind: Deployment
        metadata: 
          name: nginx-deployment 
        spec: 
          replicas: 2
          template: 
            metadata:
              labels:
                app: web_server
            spec:
              containers:
              - name: nginx
                image: nginx:1.7.9
              nodeSelector:
                disktype: ssd


Delete label from node:

        kubectl label node k8s-node1 disktype-