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

## Option 1: Run a self-contained job (go binary + Flink/Beam Job Server)

```shell
bazel run //:example-pipeline.apply
```

Inspect the logs in your terminal:

```shell
kubectl logs --follow job/example-pipeline
```

You should see output like this at the bottom:

```
2022/05/21 05:24:35 got output:
Appear: 1
Bedlam: 3
British: 5
Could: 2
Cunning: 1
Detested: 1
Did: 4
Doth: 3
Draw: 4
Drum: 1
During: 1
Edgar: 10
Exeunt: 33
Fear: 1
Filial: 1
...
```

## Option 2: Run using 

**TODO: NOT WORKING YET**

```shell
bazel run //:beam-flink-job-server.apply
bazel run //:beam-flink-job-server-service.apply
```

## Option 3: Run using Kubernetes operator

**TODO: NOT WORKING YET**

```shell
bazel run //:cert-manager.apply
```
