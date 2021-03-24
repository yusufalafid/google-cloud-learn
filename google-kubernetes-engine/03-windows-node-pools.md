1. Enable Google Containers API
```
gcloud services enable container.googleapis.com
```

2. Create Cluster
```
gcloud container clusters create k8s-1 --enable-ip-alias --num-nodes=2 --release-channel stable
```

3. Create Windows Node-pool
```
gcloud container node-pools create win-pool --cluster=k8s-1 --image-type=windows_ltsc --no-enable-autoupgrade --machine-type=n1-standard-2 --num-nodes=1
```

4. Get Cluster Credentials
```
gcloud container clusters get-credentials k8s-1
```

5. List node
```
kubetcl get nodes -o wide

---
NAME                                   STATUS   ROLES    AGE     VERSION             INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                             KERNEL-VERSION    CONTAINER-RUNTIME
gke-k8s-1-default-pool-4f6ea0e1-nn63   Ready    <none>   18m     v1.17.17-gke.1101   10.148.0.4    35.198.251.170   Container-Optimized OS from Google   4.19.150+         docker://19.3.6
gke-k8s-1-default-pool-4f6ea0e1-vlf8   Ready    <none>   18m     v1.17.17-gke.1101   10.148.0.5    35.247.167.48    Container-Optimized OS from Google   4.19.150+         docker://19.3.6
gke-k8s-1-win-pool-6eb842ff-d4vm       Ready    <none>   5m45s   v1.17.17-gke.1101   10.148.0.7    35.198.232.130   Windows Server 2019 Datacenter       10.0.17763.1757   docker://19.3.14
gke-k8s-1-win-pool-6eb842ff-k8xv       Ready    <none>   5m51s   v1.17.17-gke.1101   10.148.0.6    35.197.145.163   Windows Server 2019 Datacenter       10.0.17763.1757   docker://19.3.14
gke-k8s-1-win-pool-6eb842ff-lvsl       Ready    <none>   5m53s   v1.17.17-gke.1101   10.148.0.8    35.240.232.98    Windows Server 2019 Datacenter       10.0.17763.1757   docker://19.3.14
---
```

6. Create windows apps deployment
```
vim iis.yaml

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: iis
  labels:
    app: iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: iis
  template:
    metadata:
      labels:
        app: iis
    spec:
      nodeSelector:
        kubernetes.io/os: windows
      containers:
      - name: iis-server
        image: mcr.microsoft.com/windows/servercore/iis
        ports:
        - containerPort: 80
---

kubectl apply -f iis.yaml
```


7. Wait the deployment ready
```
kubectl get deployment --watch
```

8. Expose deployment
```
kubectl expose deployment iis --type=LoadBalancer --name iis --port 80 --target-port 80
```

9. Access service external IP
```
kubectl get svc iis
```