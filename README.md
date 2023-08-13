```bash
$ kubectl create namespace argocd
$ kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/ha/install.yaml
```

```bash
$ kubectl get svc -n argocd

NAME                                      TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.0.222.193   <none>        7000/TCP,8080/TCP            6s
argocd-dex-server                         ClusterIP   10.0.241.136   <none>        5556/TCP,5557/TCP,5558/TCP   6s
argocd-metrics                            ClusterIP   10.0.103.96    <none>        8082/TCP                     6s
argocd-notifications-controller-metrics   ClusterIP   10.0.22.86     <none>        9001/TCP                     6s
argocd-redis-ha                           ClusterIP   None           <none>        6379/TCP,26379/TCP           6s
argocd-redis-ha-announce-0                ClusterIP   10.0.150.97    <none>        6379/TCP,26379/TCP           6s
argocd-redis-ha-announce-1                ClusterIP   10.0.50.211    <none>        6379/TCP,26379/TCP           5s
argocd-redis-ha-announce-2                ClusterIP   10.0.159.15    <none>        6379/TCP,26379/TCP           5s
argocd-redis-ha-haproxy                   ClusterIP   10.0.199.124   <none>        6379/TCP                     5s
argocd-repo-server                        ClusterIP   10.0.140.61    <none>        8081/TCP,8084/TCP            5s
argocd-server                             ClusterIP   10.0.209.6     <none>        80/TCP,443/TCP               5s
argocd-server-metrics                     ClusterIP   10.0.106.93    <none>        8083/TCP                     5s
```

```bash
$ kubectl get pods -n argocd

NAME                                                READY   STATUS    RESTARTS        AGE
argocd-application-controller-0                     1/1     Running   1 (6m52s ago)   16m
argocd-applicationset-controller-5c745ddc86-jb2dv   1/1     Running   1 (6m52s ago)   16m
argocd-dex-server-5c47b8564-s8rv8                   1/1     Running   1 (6m52s ago)   16m
argocd-notifications-controller-76b59dc7d5-26kz6    1/1     Running   1 (6m52s ago)   16m
argocd-redis-ha-haproxy-5c67ffbd95-ctltg            1/1     Running   1 (6m52s ago)   16m
argocd-redis-ha-haproxy-5c67ffbd95-lwkcj            0/1     Pending   0               16m
argocd-redis-ha-haproxy-5c67ffbd95-swnxv            0/1     Pending   0               16m
argocd-redis-ha-server-0                            3/3     Running   3 (6m41s ago)   16m
argocd-redis-ha-server-1                            0/3     Pending   0               14m
argocd-repo-server-66bb5bfc97-97xkz                 0/1     Pending   0               16m
argocd-repo-server-66bb5bfc97-9f8ql                 1/1     Running   1 (6m33s ago)   16m
argocd-server-cc8556967-k5mvz                       0/1     Pending   0               16m
argocd-server-cc8556967-z58l7                       1/1     Running   1 (6m33s ago)   16m
```

### 네트워크

```bash
$ kubectl edit svc argocd-server -n argocd
```

* 로드벨런서 타입으로 변경

```bash
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```


* NodePort 타입으로 변경

```bash
$ kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```

* Port Forwarding

```bash
$ kubectl port-forward svc/argocd-server -n argocd 8080:443
```

* minikube 터널링

```bash
$ minikube service argocd-server  -n argocd
```

### 정보

* argo 비밀번호 조회

```bash
$ kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

* 시크릿 정보

```bash
$ kubectl get secret argocd-initial-admin-secret -n argocd -o json

{
    "apiVersion": "v1",
    "data": {
        "password": "cjdzdDZZbkctNnhzQlU1NQ=="
    },
    "kind": "Secret",
    "metadata": {
        "creationTimestamp": "2023-08-13T00:40:37Z",
        "name": "argocd-initial-admin-secret",
        "namespace": "argocd",
        "resourceVersion": "723",
        "uid": "0db76280-6103-490a-adce-e509114ea0d9"
    },
    "type": "Opaque"
}
```

id: admin