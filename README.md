# Kubernetes Lab

## Requirements

- Docker
- Minikube
- [Docker hub account](https://hub.docker.com/)

## Preparations

Clone kubernetes-lab using git

```bash
git clone https://github.com/dhanifudin/kubernetes-lab
```
or download using curl command

```bash
curl -L https://github.com/dhanifudin/kubernetes-lab/archive/refs/heads/main.zip -o
kubernetes-lab.zip
unzip kubernetes-lab.zip
```

## Instructions

### Building the container image

- Navigate into `hello-app` directory from `kubernetes-lab` repository.
- Build sample application using following command.

  ```bash
  # Ensure run docker build inside hello-app directory
  docker build -t <username>/hello-app:v1 .
  ```
  > Please update `<username>` value with your Docker hub account.
  > For example:
  >
  > ```bash
  > docker build -t dhanifudin/hello-app:v1 .
  > ```

- Run the `docker images` command to verify the build was successful.

  ```bash
  docker images
  ```
  > Example Output:
  > ```
  > REPOSITORY                           TAG             IMAGE ID       CREATED         SIZE
  > dhanifudin/hello-app                 v1              a9261c9d7ed0   3 hours ago     26.8MB
  > ```

### Running Container Locally

- Test container image using docker engine.
  Open new terminal then run following command.

  ```bash
  docker run --rm -p 8080:8080 <username>/hello-app:v1
  ```

- Open new terminal and run the following command to verify the container works
  and give response with `Hello, World!`.

  ```bash
  curl http://localhost:8080
  ```

  > Output:
  > ```
  > Hello, world!
  > Version: 1.0.0
  > Hostname: cee0c8bc06f0
  > ```

- After you've seen successful response, you can stop the container
by pressing `Ctrl+C` in the terminal where the `docker run` command is running.

### Push Docker Image into Registry

Docker Hub is the world's largest library and community for container images.

- Authenticate into docker registry using the following command.

  ```bash
  docker login
  ```

  > Output
  > ```
  > Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
  > Username:
  >```

- Fill your username and password then wait until you get `Login Succeeded` message

  ```
  Login Succeeded
  ```

- Push local docker image into docker registry using following command.

  ```bash
  # Format <username>/<repo-name>:<tag-name>
  # Don't forget to replace <username> with your docker hub account
  docker push <username>/hello-app:v1
  ```

### Deploying Application into Kubernetes

Kubernetes represents applications as pods, which are scalable units
holding one or more containers. Usually, you deploy pods as a set of replicas
that can be scaled. One way to deploy a set of replicas is through a Kubernetes
deployment.

- Ensure kubectl can access Kubernetes cluster by using following command.

  ```bash
  kubectl get nodes
  ```

  > If there are no problems, you should get name of your cluster. It should
  > list `minikube` (depends on your local Kubernetes).

  ```bash
  kubectl create deployment hello-app --image=<username>/hello-app:v1
  ```
- Monitor the deployment using following command.

  ```bash
  kubectl get pods
  ```

  Output

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-zpjgr   1/1     Running   0          11s
  ```

  > **Note**: We can monitor the pods using combination command of `kubectl`
  > with `watch` to get update periodically. `watch kubectl get pods`.

### Scale Application Using ReplicaSet

- ReplicaSet's purpose is to maintain a stable set of replica Pods. It is used
to guarantee the availability of a specified number of identical Pods.

- Scale up from 1 replica into 3 replica

  ```bash
  kubectl scale deployment hello-app --replicas=3
  ```

  ```bash
  kubectl get pods
  ```

  Output:

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-zpjgr   1/1     Running   0          41m
  hello-app-7464b66999-479nq   1/1     Running   0          6s
  hello-app-7464b66999-xs86r   1/1     Running   0          6s

  ```

- Simulate simple chaos engineering by delete one of available Pods. Please run
following command.

  ```bash
  # Please adjust with your available Pods name
  kubectl delete pod hello-app-7464b66999-xs86r
  ```

- The Pod with name `hello-app-7464b66999-xs86r` will be Terminated and replace
  with new Pod. This automated recovery is often referred to as self-healing.

- Scale down from 3 replica into 2 replica

  ```bash
  kubectl scale deployment hello-app --replicas=2
  ```

  ```bash
  kubectl get pods
  ```
  Output

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-zpjgr   1/1     Running   0          41m
  hello-app-7464b66999-479nq   1/1     Running   0          6s
  ```

### Expose Application

Pods have individual IP addresses, but those IPs can only be reached from
inside cluster. We need to expose Kubernetes service so it can be accessed from
outside cluster.

- Please run `kubectl expose` command to generate a Kubernetes service. We will
  map `8080` from container into port `80`.

  ```bash
  kubectl expose deployment hello-app --name=hello-app-lb --type=LoadBalancer --port 80 --target-port 8080
  ```

- Monitor the deployment of Kubernetes service by run following command.

  ```bash
  # kubectl get svc
  # or
  kubectl get services
  ```

  > **Note**: Unfortunately LoadBalancer doesn't support for local Kubernetes,
  > the EXTERNAL-IP will be in `<pending>` state. Please try expose LoadBalancer
  > service using cloud provider (GKE or EKS).

- We can expose service for local Kubernetes using ClusterIP and port forward
  combination, try run following command.

  ```bash
  # Long version command
  # kubectl expose deployment hello-app --name=hello-app --type=ClusterIP --port 80 --target-port 8080
  # Or
  # Short command. We don't need to specify type, because ClusterIP is default.
  kubectl expose deployment hello-app --port 80 --target-port 8080
  # Please run using sudo, to port forward web http 80
  sudo kubectl port-forward svc/hello-app 80:80
  ```

- Test application using curl command. Repeat the command multiple time, you
  will get different Hostname.

  ```bash
  curl http://localhost
  ```

  Output
  ```
  Hello, world!
  Version: 1.0.0
  Hostname: hello-app-65977bcd4-prvqf
  ```

### Update Application

- Modify line 49 from file `main.go` inside `hello-app` directory. Update the
  version to be `Version: 2.0.0`.

  ```bash
  # vim hello-app/main.go
  # or
  nano hello-app/main.go
  ```

- Build the application using `docker build` command, and set tag into `v2`.
  Push the image into docker hub.

  ```bash
  docker build -t <username>/hello-app:v2 .
  docker push <username>/hello-app:v2
  ```
- Monitor the Pods deployment using following command.

  ```bash
  watch kubectl get pods
  ```
  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-q5hsk   1/1     Running   0          3h14m
  ```

- Apply a rolling update to existing `hello-app` deployment with new image using
  following command.

  ```bash
  kubectl set image deployment/hello-app hello-app=<username>/hello-app:v2
  ```

- Watch the running Pods the `v1` image stop, and new Pods running `v2` imiage
  start.

  ```
  NAME                         READY   STATUS              RESTARTS   AGE
  hello-app-7464b66999-q5hsk   1/1     Running             0          3h15m
  hello-app-758d65484d-xx54z   0/1     ContainerCreating   0          8s
  ```

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-758d65484d-xx54z   1/1     Running   0          3m22s
  ```

- Test new application using `curl` command. The output will be updated into `v2`.

  ```bash
  curl http://localhost
  ```
  ```
  Hello, world!
  Version: 2.0.0
  Hostname: hello-app-758d65484d-xx54z
  ```

### Clean Up

- Delete Kubernetes service

  ```
  kubectl delete service hello-app-lb
  kubectl delete service hello-app
  ```

- Delete Kubernetes deployment
  ```
  kubectl delete deployment hello-app
  ```

### Autoscale Automatically

In Kubernetes, horizontalPodAutoscaler automatically updates a workload
resource. Horizontal scaling means that the response to increased load is to
deploy mode Pods.

- Copy the configuration below, then save into `deployment.yaml`

  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: php-apache
  spec:
    selector:
      matchLabels:
        run: php-apache
    replicas: 1
    template:
      metadata:
        labels:
          run: php-apache
      spec:
        containers:
        - name: php-apache
          image: registry.k8s.io/hpa-example
          ports:
          - containerPort: 80
          resources:
            limits:
              cpu: 500m
            requests:
              cpu: 200m
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: php-apache
    labels:
      run: php-apache
  spec:
    ports:
    - port: 80
    selector:
      run: php-apache
  ```

- Apply deployment using following command.

  ```bash
  kubectl apply -f deployment.yaml
  ```
- Kubernetes deployment can be configured using command or yaml configuration.

- Create the HorizontalPodAutoscaler using following command. The number of
  replica will be increased or decreased based on CPU utilization all Pods 50%.
  horizontalPodAutoscaler will maintains Pods between 1 and 10 replicas.

  ```bash
  kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=5
  ```

- Check the current status HorizontalPodAutoscaler using following command.

  ```bash
  # We can use hpa or horizontalpodautoscaler
  # kubectl get horizontalpodautoscaler
  kubectl get hpa
  ```

- Increase the load

  ```bash
  # Run this in a separate terminal
  # so that the load generation continues and you can carry on with the rest of the steps
  kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
  ```

- Watch the load of HorizontalPodAutoscaler

  ```bash
  # type Ctrl+C to end the watch when you're ready
  kubectl get hpa php-apache --watch
  ```

- Monitor the deployment of Pods, the number of replicaset will increased.

  ```bash
  watch kubectl get pods
  ```

- Cleanup the deployment

  ```bash
  kubectl delete hpa php-apache
  kubectl delete svc php-apache
  kubectl delete deployment php-apache
  ```

### References

- [Kubernetes Tutorial](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app)
- [Horizontal Pod Autoscale Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)
