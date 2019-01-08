# Create Service with file

## What is a Service?

Expose internal service to outside. 
Service can selete a group of Pods by label. Outside uses Service to visit the Pods.
So that Pods in the Kubernetes cluster can stop or change IP without influence outside.

## Service Configuration file

### First Create Deployments with labels

At first, we need to create Deployment Configuration files with labels.

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