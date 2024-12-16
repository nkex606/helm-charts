# nginx ingress controller on GCP

ingress controller can be `external` or `internal`

fisrt add nginx chart to helm repo

```bash
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx 
```

## external(default)

```bash
$ helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```

## internal 

after install external ingress controller, follow [document](https://kubernetes.github.io/ingress-nginx/user-guide/multiple-ingress/) to install an internal ingress controller

override values can be found at `override-values-internal.yaml`

```bash
$ helm upgrade --install ingress-nginx-internal ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  -f ./ingress-nginx/values-override-internal.yaml
```

## verified

```bash
$ kubectl get svc -n ingress-nginx
```

there shoule be two svc, one with external address, the other one with internal address