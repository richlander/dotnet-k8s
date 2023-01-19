# Using .NET with Kubernetes

These instruction use [minikube](https://minikube.sigs.k8s.io/) but should work with any compatible environment.

You can host a .NET sample with a [few quick commands](hello-dotnet/README.md) at localhost:8080:

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/hello-dotnet/hello-dotnet.yaml
kubectl port-forward service/hello-dotnet 8080:80
```

There are many ways to do things with Kubernetes. A small subset are covered here to help you get started.

- [Single node app](hello-dotnet/README.md)

