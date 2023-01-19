# Single node app

Host a .NET sample with a few quick commands.

```bash
kubectl create deployment hello-dotnet --image mcr.microsoft.com/dotnet/samples:aspnetapp
kubectl expose deployment hello-dotnet --type=NodePort --port=80
kubectl port-forward service/hello-dotnet 8080:80
```

Alternatively, apply a ready-made manifest to create the required resources.

```bash
kubectl apply -f https://raw.githubusercontent.com/richlander/dotnet-k8s/main/hello-dotnet/hello-dotnet.yaml
```

If you've cloned the repo, the local file can be applied instead:

```bash
kubectl apply -f hello-dotnet.yaml
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