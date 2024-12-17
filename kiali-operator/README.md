# kiali

官方建議的安裝方法是安裝 **Kiali Operator**, 不管是用 [Helm Chart](https://kiali.io/docs/installation/installation-guide/install-with-helm/) 或是 [OperatorHub](https://kiali.io/docs/installation/installation-guide/installing-with-operatorhub/)


Kiali Helm Chart 提供兩種 Chart, 建議直接安裝 `kiali-operator`

- kiali-operator: 同時安裝 operator 以及 kiali CR
- kiali-server: 只安裝 standalone kiali, 不安裝 operator & kiali CR

## install Kiali

新增 kiali helm chart repo
```bash
$ helm repo add https://kiali.org/helm-charts
```

已經加入過可以直接更新repo

```bash
$ helm repo update
```

安裝 kiali operator

```bash
$ helm upgrade --install kiali-operator -n kiali-operator --create-namespace -f ./kiali-operator/override-values.yaml kiali/kiali-operator
```

## override-values.yaml

使用下面指令可以先看到預設的 `values.yaml`

```bash
$ helm show values kiali/kiali-operator
```

修改後的 `override-values.yaml`

```yaml
cr:
  create: true # 建立一個 kiali CR
  namespace: istio-system # 在 istio-system 建立 kiali CR
  spec:
    server:
      web_fqdn: <kiali host url> # kiali server url
      web_root: /
      web_schema: http
    deployment:
      ingress:
        enabled: true # 預設不會建立 ingress, 需要自行 enable
        class_name: nginx-internal # use internal
        override_yaml: # ingress spec 
          spec:
            rules:
            - host: <kiali host url>
              http:
                paths:
                - path: /
                  backend:
                    service:
                      name: kiali
                      port: 
                        number: 20001
                  pathType: Prefix
    external_services: # 其他 services 定義在這
      istio: # istio components status，可以參考 https://github.com/kiali/kiali-operator/blob/master/crd-docs/cr/kiali.io_v1alpha1_kiali.yaml#L263
        root_namespace: istio-system
        component_status:
          enabled: true
          components:
            - app_label: istiod
              is_core: true
            - app_label: istio-ingress
              is_core: true
              is_proxy: true
              namespace: istio-ingress
      prometheus: # 預設會用 prometheus istio-addons 的路徑，改成自行安裝的 prometheus operator service 路徑
        url: http://prom-operator-kube-prometh-prometheus.monitor:9090
      grafana: # 同 prometheus, 如果 grafana 沒有安裝在 kubernetes cluster 內，改用 url: http://grafana.example.com
        in_cluster_url: http://prom-operator-grafana.monitor
```

## login token

kiali 預設使用 `token` 登入，官方的做法是幫 service account `kiali-service-account` 手動產生一個 login token

```bash
$ kubectl create token -n istio-system kiali-service-account
```

產生出來的 JWT token 可以直接用來登入 kiali, 但是只有一小時的時效，如果要產生永久時效的 token, 需要自己建立一個 secret

```yaml!
# secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: kiali-long-lived-token
  namespace: istio-system
  annotations:
    kubernetes.io/service-account.name: kiali-service-account
type: kubernetes.io/service-account-token
```

```bash
$ kubectl get secret -n istio-system

NAME                     TYPE                                  DATA   AGE
kiali-long-lived-token   kubernetes.io/service-account-token   3      21m
```