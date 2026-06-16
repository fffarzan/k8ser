# Notes

### first

After creating mongo secrets, we should apply it af first:

```bash
kubectl apply -f mongo-secret.yaml
kubectl get secret
```

after that the secret can be referenced in Deployment file. And after that:

```bash
kubectl apply -f mongo-deployment.yaml
```

That explanation above is also valid for configMap

### second

```bash
minikube service [servicename] # assings to an external service a public IP address
```
