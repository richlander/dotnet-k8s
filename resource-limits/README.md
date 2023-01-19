# Resource-limited app

Limit memory and CPU for an app.

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/resource-limits/dotnet-resource-limits.yaml
```

If you've cloned the repo, the local file can be applied instead:

```bash
kubectl apply -f dotnet-resource-limits.yaml
```

Create a proxy to the service. View the sample app at http://localhost:8080/.

```bash
kubectl port-forward service/hello-dotnet 8080:80
```

View the active resources that have been deployed and then delete them.

```bash
kubectl get pod
kubectl get service
kubectl get deployment
kubectl delete service hello-dotnet
kubectl delete deployment hello-dotnet
```
