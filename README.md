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

## Option 2: Run local binary using a job server in a cluster

**TODO: NOT WORKING YET**

```shell
bazel run //:beam-flink-job-server.apply
bazel run //:beam-flink-job-server-service.apply
```

Rune `kubefwd` in a terminal:

```shell
sudo -E <PATH_TO_KUBEFWD> services \
  --kubeconfig <PATH_TO_HOME>/.kube/config \
  --namespace kubernetes-dashboard \
  --namespace default \
  --namespace kube-system
```

fails:

```shell
bazel run :beam-go-k8s -- \
  --runner flink \
  --endpoint beam-flink-job-server.default:8099 \
  --output /tmp/output-flink.txt  \
  --environment_type LOOPBACK
```

## Option 3: Run using Kubernetes operator

**TODO: NOT WORKING YET**

### Set up the Apace Flink Kubernetes operator

I would like to figure out how to use
[github.com/apache/flink-kubernetes-operator](https://github.com/apache/flink-kubernetes-operator)
to run jobs.

```shell
bazel run //:cert-manager.apply
```

Install the operator:

```shell
helm repo add flink-operator-repo https://downloads.apache.org/flink/flink-kubernetes-operator-0.1.0/
helm install flink-kubernetes-operator flink-operator-repo/flink-kubernetes-operator
```

(TODO: Install this with bazel commands to avoid needing
to install helm locally.)

### TODO: Run job using Kubernetes operator
