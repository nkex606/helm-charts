# nginx ingress controller on AWS

ingress controller can be `external` or `internal`

fisrt add nginx chart to helm repo

```bash
$ helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx 
```

## external(NLB)

```bash
$ helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx  \
  --namespace ingress-nginx --create-namespace \
  -f ./ingress-nginx/override-values-nlb.yaml
```

## internal

```bash
$ helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx  \
  --namespace ingress-nginx --create-namespace \
  -f ./ingress-nginx/override-values-internal-nlb.yaml
```

annotation reference can be found at https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.2/guide/service/annotations/