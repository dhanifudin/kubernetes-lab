# Minikube Installation

## Install Docker CE

> Skip this steps if docker already installed.

### Setup the repository

Install the `yum-utils` package (which provides `yum-config-manager` utility)
then add docker-ce repository with the following command.

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### Install Docker Engine

Install the latest version of Docker Engine and docker tools with the following
command.

```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

### Enable Service

Start Docker service using `systemctl` command.

```bash
sudo systemctl start docker
```

Ensure the Docker service running by check the status.

```bash
sudo systemctl status docker
```

Test docker command using following command.

```bash
sudo docker run hello-world
```

> If there are no problems, you should get long message from hello-docker
> container.

## Install Minikube

Download latest stable release of minikube for rpm package version using following command.

```bash
# Download the rpm package
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
# Install local rpm package
sudo rpm -Uvh minikube-latest.x86_64.rpm
```

Add your user into `docker` group, to enable run `docker` command without `sudo`.
Please run the following command.

```bash
sudo usermod -aG docker $USER && newgrp docker
```

Setup kubectl command using alias.

```bash
echo 'alias kubectl="minikube kubectl --"' >> ~/.bashrc
```

> Alternative way, you can open your favorite editor to modify `~/.bashrc` then
> put `alias kubectl="minikube kubectl --"` on the bottom file.
> ```
> # vim ~/.bashrc
> # or
> nano ~/.bashrc
> ```

Source your terminal session then run following command or open new terminal
session.

```bash
source ~/.bashrc
```

Start the minikube using `minikube start`. Please run using user with
administrator access (`sudo` access). **Please don't using user `root`**.

```bash
minikube start
```

Check the cluster using following command.

```bash
kubectl get nodes
```

Output
```
NAME      STATUS    ROLES           AGE   VERSION
minikube  Ready     control-plane   23m   v1.25.3
```

> If you get output above, the installation of minikube is complete.

<!-- ## Deployment -->

<!-- ```bash -->
<!-- kubectl create deployment hello-minikube --image=docker.io/nginx:1.23 -->
<!-- kubectl expose deployment hello-minikube --type=NodePort --port=80 -->
<!-- ``` -->

<!-- ## Enable LoadBalancer -->

<!-- ```bash -->
<!-- kubectl create deployment balanced --image=docker.io/nginx:1.23 -->
<!-- kubectl expose deployment balanced --type=LoadBalancer --port=80 -->
<!-- ``` -->

<!-- ```bash -->
<!-- minikube tunnel -->
<!-- ``` -->

<!-- ```bash -->
<!-- kubectl get services balanced -->
<!-- ``` -->
