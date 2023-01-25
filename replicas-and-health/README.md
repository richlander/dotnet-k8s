# Replicas backed by health-checks

Use multiple replicas and health checks for reliability.

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/replicas-and-health/replica-health.yaml
```

If you've cloned the repo, the local file can be applied instead:

```bash
kubectl apply -f replica-health.yaml
```

Start viewing pods.

```bash
kubectl get -w pod
```

In another terminal, create a proxy to the service. View the sample app at http://localhost:8080/.

```bash
kubectl port-forward service/hello-dotnet 8080:80
```

Resources can be deleted using the following pattern:

```bash
kubectrl delete -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/replicas-and-health/replica-health.yaml
```
