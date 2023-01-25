# Using .NET with Kubernetes

These instruction should work with any [kubectl](https://kubernetes.io/docs/reference/kubectl/) compatible environment.

You can host a .NET sample with a [few quick commands](hello-dotnet/README.md) at localhost:8080:

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/hello-dotnet/hello-dotnet.yaml
kubectl port-forward service/hello-dotnet 8080:80
```

After you are done, you can delete the resources:

```bash
kubectl delete -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/hello-dotnet/hello-dotnet.yaml
```

There are many ways to do things with Kubernetes. A small subset are covered here to help you get started.

- [Single node app](hello-dotnet/README.md)
- [Resource limits](resource-limits/README.md)
- [Replicas and health checks](health-and-replicas/README.md)
