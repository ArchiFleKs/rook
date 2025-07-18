---
title: Developer Environment
---

## Install Kubernetes

You can choose any Kubernetes install of your choice. The test framework only depends on `kubectl`
being configured. To install `kubectl`, please see the [official guide](https://kubernetes.io/docs/tasks/tools/#kubectl).

## Minikube

The developers of Rook are working on Minikube and thus it is the recommended way to quickly get
Rook up and running. Minikube should not be used for production but the Rook authors
consider it great for development. While other tools such as k3d/kind are great, users have faced
issues deploying Rook.

**Always use a virtual machine when testing Rook. Never use your host system where local devices may mistakenly be consumed.**

To install Minikube follow the [official
guide](https://minikube.sigs.k8s.io/docs/start/). It is recommended to use the
qemu2 driver when running on a Linux machine and the hyperkit driver when running on a MacOS. Both
allow to create and attach additional disks to the virtual machine. This is required for the Ceph
OSD to consume one drive.  We don't recommend any other drivers for Rook. You will need a Minikube
version 1.23 or higher.

Starting the cluster on Minikube is as simple as running:

```console
# On Linux
minikube start --disk-size=40g --extra-disks=1 --driver qemu2

# On MacOS with Intel processor
minikube start --disk-size=40g --extra-disks=1 --driver hyperkit

# On MacOS with Apple silicon
minikube start --disk-size=40g --extra-disks 1 --driver qemu
```

It is recommended to install a Docker client on your host system too. Depending on your operating
system follow the [official guide](https://docs.docker.com/engine/install/binaries/).

Stopping the cluster and destroying the Minikube virtual machine can be done with:

```console
minikube delete
```

## Install Helm

Install the `helm` tool to use the helm charts rook provides. Depending on your operating system,
install it from system packages or follow the [official Helm guide](https://helm.sh/docs/intro/install/).


## Using local Rook image on minikube cluster

Developers can test quickly their changes by building and using the local Rook image
on their minikube cluster.

1) Set the local Docker environment to use minikube:

    ```console
    eval $(minikube docker-env -p minikube)
    ```

2) Build your local Rook image. The following command will generate a Rook image
labeled in the format `local/ceph-<arch>`.

    ```console
    cd <your_rook_src_directory>
    make BUILD_REGISTRY=local
    ```

3) Tag the generated image as `rook/ceph:master` so operator will pick it.

    ```console
    docker tag "local/ceph-$(go env GOARCH)" 'rook/ceph:master'
    ```

4) Create a Rook cluster in minikube, or if the Rook cluster is already configured, apply the new
operator image by restarting the operator.


## Creating a dev cluster

To accelerate the development process, users have the option to employ the script located
at `tests/scripts/create-dev-cluster.sh`. This script is designed to rapidly set
up a new minikube environment, apply the CRDs and the common file, and then utilize the
`cluster-test.yaml` script to create the Rook cluster. Once setup, users can use the different `*-test.yaml`
files from the `deploy/examples/` directory to configure their clusters. This script supports
the possibility of creating multiple rook clusters running on the same machine by using the option
`-p <profile-name>`.
