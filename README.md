# Cozystack GitOps repo example


This repo is created to show how to configure [Cozystack](https://github.com/aenix-io/cozystack) platform using GitOps approach


## FluxCD Configuration

To use this repo with FluxCD you need to configure GitRepo in Cozystack:

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: infra-main
  namespace: tenant-root
spec:
  url: https://github.com/aenix-io/cozystack-gitops-example
  ref:
    branch: main
  interval: 1m0s
  timeout: 60s
  #secretRef:
  #  name: infra-main
```

Optionally you can specify token for accessing this repo from private Gitlab repository:

```yaml
apiVersion: v1
metadata:
  name: infra-main
  namespace: tenant-root
stringData:
  password: token-string-here123
  username: pat
kind: Secret
```

Kustomize resource for every cluster:

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: infra-main-cluster1
  namespace: tenant-root
spec:
  force: false
  interval: 10m0s
  path: ./clusters/cluster1
  prune: true
  sourceRef:
    kind: GitRepository
    name: infra-main
```

### Check configuration

```bash
# kubectl get gitrepositories  -n tenant-root
NAME         URL                                                    AGE   READY   STATUS
infra-main   https://github.com/aenix-io/cozystack-gitops-example   21s   True    stored artifact for revision 'main@sha1:c413c728b036996164278957e7f93a3276076f42'

# kubectl get kustomizations -n tenant-root
NAME                  AGE   READY   STATUS
infra-main-cluster1   13s   False   GitRepository.source.toolkit.fluxcd.io "main" not found

# kubectl get helmreleases -n tenant-root
NAME                      AGE     READY   STATUS
tenant-example            7h27m   True    Helm install succeeded for release tenant-root/tenant-example.v1 with chart tenant@1.3.0
tenant-example-services   7h27m   True    Helm upgrade succeeded for release tenant-root/tenant-example-services.v15 with chart staging@0.1.0+a9b790b5ffe2

# kubectl get helmreleases -n tenant-example
NAME                                 AGE     READY   STATUS
clickhouse-service                   6h50m   True    Helm install succeeded for release tenant-example/clickhouse-service.v1 with chart clickhouse@0.2.1
kafka-service                        6h50m   True    Helm upgrade succeeded for release tenant-example/kafka-service.v2 with chart kafka@0.2.2
kubernetes-service                   6h34m   True    Helm upgrade succeeded for release tenant-example/kubernetes-service.v2 with chart kubernetes@0.8.0
kubernetes-service-cert-manager      6h34m   True    Helm install succeeded for release cozy-cert-manager/cert-manager.v1 with chart cozy-cert-manager@0.10.0
kubernetes-service-cilium            6h34m   True    Helm install succeeded for release cozy-cilium/cilium.v1 with chart cozy-cilium@0.10.0
kubernetes-service-copy-secrets      5h59m   True    Helm upgrade succeeded for release default/default-kubernetes-service-copy-secrets.v5 with chart secret@1.0.2+a9b790b5ffe2
kubernetes-service-csi               6h34m   True    Helm install succeeded for release cozy-csi/csi.v1 with chart cozy-kubevirt-csi-node@0.10.0
kubernetes-service-fluxcd            6h34m   True    Helm install succeeded for release cozy-fluxcd/fluxcd.v1 with chart cozy-fluxcd@0.10.0
kubernetes-service-fluxcd-operator   6h34m   True    Helm install succeeded for release cozy-fluxcd/fluxcd-operator.v1 with chart cozy-fluxcd-operator@0.10.0
postgres-service                     6h50m   True    Helm upgrade succeeded for release tenant-example/postgres-service.v2 with chart postgres@0.4.0
rabbitmq-service                     6h50m   True    Helm install succeeded for release tenant-example/rabbitmq-service.v1 with chart rabbitmq@0.2.0
```

## ArgoCD Configuration

TODO
