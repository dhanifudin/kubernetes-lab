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
  > $ curl http://localhost:8080
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
  kubectl get pods
  ```

  > If there are no problems, you should get message `No resources found in
  > default namespace.` or list available of pods in default namespace.

  ```bash
  kubectl create deployment hello-app --image=<username>/hello-app:v1
  ```
- Monitor the deployment by using following command.

  ```bash
  kubectl get pods
  ```

  Output

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-zpjgr   1/1     Running   0          11s
  ```

  > **Note**: We can monitor the pods using combination `kubectl` with `watch`
  > to get update periodically. `watch kubectl get pods`.

### Scale Application Using Replica Set

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

  ```bash
  kubectl scale deployment hello-app --replicas=2
  ```

  ```bash
  kubectl get pods
  ```

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-zpjgr   1/1     Running   0          41m
  hello-app-7464b66999-479nq   1/1     Running   0          6s
  ```

### Expose Application

  ```bash
  kubectl expose deployment hello-app --name=hello-app-lb --type=LoadBalancer --port 80 --target-port 8080
  ```

  ```bash
  kubectl get services
  ```

### Update Application

  ```bash
  watch kubectl get pods
  ```
  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-7464b66999-q5hsk   1/1     Running   0          3h14m
  ```

  ```bash
  kubectl set image deployment/hello-app hello-app=<username>/hello-app:v2
  ```

  ```
  NAME                         READY   STATUS              RESTARTS   AGE
  hello-app-7464b66999-q5hsk   1/1     Running             0          3h15m
  hello-app-758d65484d-xx54z   0/1     ContainerCreating   0          8s
  ```

  ```
  NAME                         READY   STATUS    RESTARTS   AGE
  hello-app-758d65484d-xx54z   1/1     Running   0          3m22s
  ```

### Clean Up

  ```
  kubectl delete service hello-app-lb
  ```

  ```
  kubectl delete deployment hello-app
  ```

### Deploy AutoScale HorizontalPodAutoscaling

  ```bash
  kubectl autoscale deployment hello-app --cpu-percent=60 --min=1 --max=5
  ```

### References

- [Kubernetes Tutorial](https://cloud.google.com/kubernetes-engine/docs/tutorials/hello-app)
- [Horizontal Pod Autoscale Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)
