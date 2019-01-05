# Create Job with file

## What is a job

Only run one time

## Job Configuration file

Job Configuration file:

        apiVersion: batch/v1
        kind: Job
        metadata: 
          name: myjob
        spec:
          completions: 6
          parallelism: 2
          template:
            metadata: 
              name: myjob
            spec:
              containers:
              - name: hello
                image: busybox
                command: ["echo", "hello k8s job!"]
              restartPolicy: Never

* parallelism: run the job parallel, default value: 1
* completions: mark as complete until 6 pods are started. default value: 1

__Note__: For Job, it's `restartPolicy` could either be `Never` or `OnFailure`. But for `Deployment`, can be set to `Always`

* `Never`: Start a new container when failure rather than restart the failure one.
* `OnFailure`: Once the container running failure, restart it.

## Cron Job Configuration file

Cron Job Configuration file:

        apiVersion: batch/v2alpha1
        kind: CronJob
        metadata:
          name: hello
        spec:
          schedule: "*/1 * * * *"
          jobTemplate: 
            spec: 
              template:
                spec:
                  containers:
                  - name: hello
                    image: busybox
                    command: ["echo", "hello k8s job!"]
                  restartPolicy: OnFailure