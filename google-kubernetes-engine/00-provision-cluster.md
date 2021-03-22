1. Set default project
```
$ gcloud config set project playground-s-11-73835160

---
Updated property [core/project].
---
```

2. Set compute zone 
Compute zone [list](https://cloud.google.com/compute/docs/regions-zones#available)
```
$ gcloud config set compute/zone asia-southeast1-a

---
Updated property [compute/zone].
---
```

3. Enable Google Container Services
```
$ gcloud services enable container.googleapis.com

---
Operation "operations/acf.p2-98174380011-113d48c4-96d5-457e-9060-da78eaeaf63b" finished successfully.
---
```
4. Get Cluster configuration
```
$ gcloud container get-server-config

---
Fetching server config for asia-southeast1-a
channels:
- channel: RAPID
  defaultVersion: 1.19.7-gke.2503
  validVersions:
  - 1.20.4-gke.400
  - 1.19.8-gke.1000
  - 1.19.7-gke.2503
- channel: REGULAR
  defaultVersion: 1.18.12-gke.1210
  validVersions:
  - 1.18.15-gke.1501
  - 1.18.12-gke.1210
- channel: STABLE
  defaultVersion: 1.17.17-gke.1101
  validVersions:
  - 1.17.17-gke.1101
  - 1.17.17-gke.1100
  - 1.16.15-gke.7801
defaultClusterVersion: 1.17.17-gke.1101
defaultImageType: COS
validImageTypes:
- COS
- UBUNTU
- COS_CONTAINERD
- UBUNTU_CONTAINERD
- WINDOWS_SAC
- WINDOWS_LTSC
validMasterVersions:
- 1.18.16-gke.500
- 1.18.16-gke.300
validNodeVersions:
- 1.18.16-gke.500
- 1.18.16-gke.300
---
```

5. Create GKE cluster
```
$ gcloud container clusters create gke-1 --num-nodes=1 --cluster-version=1.17.17-gke.1101 --image-type=UBUNTU

---
NAME   LOCATION           MASTER_VERSION    MASTER_IP       MACHINE_TYPE  NODE_VERSION      NUM_NODES  STATUS
k8s-1  asia-southeast1-a  1.17.17-gke.1101  35.185.185.239  e2-medium     1.17.17-gke.1101  1          RUNNING
---
```

6. Get cluster credentials
```
$ gcloud container clusters get-credentials k8s-1

---
Fetching cluster endpoint and auth data.
kubeconfig entry generated for k8s-1.
---
```

7. Test cluster credentials
```
$ kubectl get nodes

---
NAME                                   STATUS   ROLES    AGE     VERSION
gke-k8s-1-default-pool-92a66944-mjfd   Ready    <none>   8m12s   v1.17.17-gke.1101
---
```

8. Create hello deployment
```
$ kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0

---
deployment.apps/hello-server created
---
```

9. Expose deployment
```
$ kubectl expose deployment hello-server --type LoadBalancer --port 80 --target-port 8080
---
service/hello-server exposed
---
```

10. Access `hello-server` deployment.
```
$ kubectl get svc hello-server

---
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
hello-server   LoadBalancer   10.43.241.119   34.126.118.29   80:31021/TCP   92s
---

Access to using web browser or curl : 34.126.118.29
```

11. Clean up resources
```
$ kubectl delete deployment hello-server

---
deployment.apps "hello-server" deleted
---

$ gcloud container clusters delete k8s-1
---
The following clusters will be deleted.
 - [k8s-1] in [asia-southeast1-a]

Do you want to continue (Y/n)?  y

Deleting cluster k8s-1...done.
Deleted [https://container.googleapis.com/v1/projects/playground-s-11-73835160/zones/asia-southeast1-a/clusters/k8s-1].
---
```