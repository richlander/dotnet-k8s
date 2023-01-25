# Resource-limited app

Limit memory and CPU for an app.

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/resource-limits/resource-limits.yaml
```

If you've cloned the repo, the local file can be applied instead:

```bash
kubectl apply -f resource-limits.yaml
```

See resource limits for the deployment.

```bash
kubectl describe deployment
```

Create a proxy to the service. View the sample app at http://localhost:8080/.

```bash
kubectl port-forward service/hello-dotnet 8080:80
```

Resources can be deleted using the following pattern:

```bash
kubectrl delete -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/resource-limits/resource-limits.yaml
```
