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

### Running container locally

### Push Docker Image into Registry

### Deploying Sample App into Kubernetes

### Scale App

### Deploy AutoScale Rule

### Expose Application

### Update Application

### Clean Up
