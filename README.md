# Bitnami NATS

- Concept https://www.youtube.com/watch?v=FugnvZs012Y

- values.yaml
```yaml
replicaCount: 3

auth:
  enabled: true
  username: natsuser
  password: natspassword

cluster:
  enabled: true
  replicas: 3
  name: nats-cluster
  auth:
    enabled: true
    username: clusteruser
    password: clusterpassword

resources:
  limits:
    cpu: 500m
    memory: 256Mi
  requests:
    cpu: 250m
    memory: 128Mi

persistence:
  enabled: true
  storageClass: standard
  accessModes:
    - ReadWriteOnce
  size: 8Gi 
```

-  Helm chart Bitnami NATS
```commandline
helm repo ls
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm search repo bitnami
helm search repo bitnami/nats
helm show values bitnami/nats
helm search repo bitnami/nats
helm search repo bitnami/nats --versions
helm show chart bitnami/nats --version 8.2.2
helm show readme bitnami/nats --version 8.2.2
helm show values bitnami/nats --version 8.2.2
helm show all bitnami/nats --version 8.2.2

helm upgrade --install nats bitnami/nats \
  --version 8.2.2 \
  -f values.yaml \
  --namespace nats \
  --create-namespace \
  --wait 
```

- ArgoCD Application
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nats
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://charts.bitnami.com/bitnami
    chart: nats
    targetRevision: 8.2.2
    helm:
      values: |
        replicaCount: 3

        auth:
          enabled: true
          username: natsuser
          password: natspassword

        cluster:
          enabled: true
          replicas: 3
          name: nats-cluster
          auth:
            enabled: true
            username: clusteruser
            password: clusterpassword

        resources:
          limits:
            cpu: 500m
            memory: 256Mi
          requests:
            cpu: 250m
            memory: 128Mi

        persistence:
          enabled: true
          storageClass: standard
          accessModes:
            - ReadWriteOnce
          size: 8Gi
  destination:
    server: https://kubernetes.default.svc
    namespace: nats
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

- Internal NATS Cluster Access
```commandline
nats.nats.svc.cluster.local:4222
```

- Grafana Dashboard https://grafana.com/grafana/dashboards/14725-nats-jetstream/
