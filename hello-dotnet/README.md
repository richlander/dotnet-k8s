# `hello-dotnet` single node app

You can host a .NET sample with a few quick commands.

```bash
kubectl create deployment hello-dotnet --image mcr.microsoft.com/dotnet/samples:aspnetapp
kubectl expose deployment hello-dotnet --type=NodePort --port=80
kubectl port-forward service/hello-dotnet 8080:80
```

You should be able to now view the sample app at http://localhost:8080/ (if you are testing this on your local machine).

You can see the resource that you've deployed and then delete them:

```bash
kubectl get pod
kubectl get service
kubectl get deployment
kubectl delete service hello-dotnet
kubectl delete deployment hello-dotnet
```

You can also deploy the same app via the included manifest.

```bash
kubectl apply -f hello-dotnet.yaml
kubectl port-forward service/hello-dotnet 8080:80
```
