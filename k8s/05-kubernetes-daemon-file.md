# Create Daemon with file

## What is a Daemon?

Run at most one instance per node.

## Daemon Configuration file

Daemon Configuration file:

        apiVersion: extensions/v1beta1
        kind: DaemonSet
        metadata:
        name: node-exporter-daemonset
        spec:
        template:
            metadata:
            labels:
                app: prometheus
            spec:
            hostNetwork: true
            containers:
            - name: node-exporter
                image: prom/node-exporter
                imagePullPolicy: IfNotPresent
                command:
                - /bin/node_exporter
                - --path.procfs
                - /host/proc
                - --path.sysfs
                - /host/sys
                - --collector.filesystem.ignored-mount-points
                - ^/(sys|proc|dev|host|etc)($|/)
                volumeMounts:
                - name: proc
                mountPath: /host/proc
                - name: sys
                mountPath: /host/sys
                - name: root
                mountPath: /rootfs
            volumes:
            - name: proc
                hostPath:
                path: /proc
            - name: sys
                hostPath:
                path: /sys
            - name: root
                hostPath:
                path: /

