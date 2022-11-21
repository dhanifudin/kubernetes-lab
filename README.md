# Kubernetes Lab

## Requirements

- Docker
- Minikube
- [Docker hub account](https://hub.docker.com/)

## Preparations

Clone kubernetes-lab

```bash
git clone https://github.com/dhanifudin/kubernetes-lab
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

```
REPOSITORY                           TAG             IMAGE ID       CREATED         SIZE
dhanifudin/hello-app                 v1              a9261c9d7ed0   3 hours ago     26.8MB
```

### Running Container Locally (optional)

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

- After you've seen successful response, you can stop the container
by pressing `Ctrl+C` in the terminal where the `docker run` command is running.

### Push Docker Image into Registry

Docker Hub is the world's largest library and community for container images.

- Authenticate into docker registry using the following command.

```bash
docker login
```

```bash
# Output
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username:
```

- Fill your username and password then wait until `Login Succeeded` message

```
Login Succeeded
```

- Push local docker image into docker registry using following command.

```bash
# Format <username>/<repo-name>:<tag-name>
# Don't forget to replace <username> with your docker hub account
docker push <username>/hello-app:v1
```

### Deploying Sample App into Kubernetes

### Scale App

### Deploy AutoScale Rule

### Expose Application

### Update Application

### Clean Up
