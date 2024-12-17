# Loki

## 安裝說明
Getting started with overrides value file for deploying Loki using the **Simple Scalable architecture** in GKE and using GCS.

### 需要：
1. service account 且擁有以下權限： **Storage Object Admin**, **Storage Object Creator**, **Storage Admin**
2. Importing SA credential as a Secret
3. gcp 建立所需的 buckets (e.g. chunks, ruler, admin)
4. 見 override-values.yaml，將上述 2. 3. 資料導入  

### 安裝：
```bash
$ helm install loki grafana/loki -f ./loki/override-values.yaml -n loki --create-namespace
```


## 以下[官方說明](https://github.com/grafana/loki/tree/main/production/helm/loki/docs/examples/oss)參考

> ## Installation of Helm Chart
> These instructions assume you have already have access to a Kubernetes cluster, GCS Bucket and GCP Service Account which has read/write permissions to that GCS Bucket.
> 
> ### Populate Secret Values
> Populate the examples/enterprise/enterprise-secrets.yaml so that:
> - The gcp_service_account.json secret has the contents of your GCP Service Account JSON key
> 
> Deploy the secrets file to your k8s cluster.
> 
> `kubectl apply -f loki-secrets.yaml`
> 
> ### Configure the Helm Chart
> Open examples/enterprise/overides-oss-gcs.yaml and replace `{YOUR_GCS_BUCKET}` with the name of your GCS bucket. If there are other things you'd like to configure, view the core [Values.yaml file](https://github.com/grafana/helm-charts/blob/main/charts/loki-simple-scalable/values.yaml) and override anything else you need to within the overrides-enterprise-gcs.yaml file.
> 
> ### Install the Helm chart
> 
> `helm upgrade --install --values {PATH_TO_YOUR_OVERRIDES_YAML_FILE} {YOUR_RELEASE_NAME} grafana/loki-simple-scalable --namespace {KUBERNETES_NAMESPACE}`
