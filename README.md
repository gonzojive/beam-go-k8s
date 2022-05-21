# beam-go-k8s

Minimal working example using Beam, Go, Apache Flink, Bazel, and Kubernetes.

## Minikube setup

This example is tested with `minikube`, but it should work for other Kubernetes
distributions as well.

See [hello-minikube](https://kubernetes.io/docs/tutorials/hello-minikube/)   for
a tutorial.

Start minikube:

```shell
minikube start
```

To build container images from this repository and run them in the minikube, set
up a local registry:

```shell
minikube addons enable registry
```

To get a local registry working, I typically [follow minikube's instructions for
MacOS](https://minikube.sigs.k8s.io/docs/handbook/registry/#enabling-insecure-registries), even though I run minikube on Ubuntu Linux:

```shell
docker run --rm -it --network=host alpine ash -c "apk add socat && socat TCP-LISTEN:5000,reuseaddr,fork TCP:$(minikube ip):5000"
```

## Create the Kubernetes resources

```shell
bazel run //:cert-manager.apply
bazel run //:beam-flink-job-server.apply
bazel run //:beam-flink-job-server-service.apply
```
