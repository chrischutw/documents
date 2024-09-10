# Helmfile Cheatsheet

## Install helm-diff for using helmfile

```shell
helm plugin install https://github.com/databus23/helm-diff
```

## Install Nginx Ingress

```shell
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
helm install nginx nginx-stable/nginx-ingress
```

```shell
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm install sealedsecrets sealed-secrets/sealed-secrets
```

## Destroy deployment

```shell
helm destroy -n prod 
```

## Install inventory-discovery

- install helm-diff for using helmfile

```shell
helm plugin install https://github.com/databus23/helm-diff
```

- install sealed secret controller

```shell
cd ~/work/helmfile/devops/apps/sealed-secrets
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helmfile apply
```

- Create new sealed secret file

```shell
k -n inventory-discovery create secret generic $secret_name --from-literal=postgresql-password=test -o yaml --dry-run=client | \
 kubeseal \
 --controller-name=sealed-secrets \
 --controller-namespace=common \
 --format yaml > `$secreat.yaml`
```

- Apply secret file to k8s

```shell
k -n common apply -f $secreat.yaml
```

- Deploy inventory-discovery

```shell
cd ~/work/helmfile/data-warehouse/apps/inventory-discovery
helmfile --kube-context=docker-desktop -e prod sync --set springboot.image.tag=release_1.0.14
```

## Nginx Ingress with tls config

- Install SSL

```shell
k -n common create secret tls $tls_name --cert=$crt --key=$key -o yaml --dry-run | \
 kubeseal \
 --controller-name=sealed-secrets \
 --controller-namespace=common \
 --format yaml > $tls_name.yaml
```

- Config

```shell
controller:
  service:
    type: LoadBalancer

controller:
  service:
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
      service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout: "10"      
    type: LoadBalancer
    externalTrafficPolicy: Local
    omitClusterIP: true # 沒設定時，upgrade helm chart 會失敗
  extraArgs:
    default-ssl-certificate: "common/tenmax-tls" # 設定預設的 ssl certificate

defaultBackend:
  service:
    omitClusterIP: true # 沒設定時，upgrade helm chart 會失敗
  enabled: false

```
