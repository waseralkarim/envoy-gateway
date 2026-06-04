### Install Prometheus (remote write)

Create values.yaml:

```jsx
prometheus:
  prometheusSpec:
    externalLabels:
      cluster: example-k3s
    remoteWrite:
      - url: "http://192.168.xx.xx:9090/api/v1/write"

grafana:
  enabled: false
```

Install:

```jsx
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  -n monitoring --create-namespace \
  -f values.yaml
```
