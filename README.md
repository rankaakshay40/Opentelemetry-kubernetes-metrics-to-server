**Run the following commands from your shell to install kube-state-metrics into the default namespace of your Kubernetes Cluster:**

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install ksm prometheus-community/kube-state-metrics -n "default"

**Install openteletry collector via helm chart as deployment**

helm repo add openTelemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update

helm install otel-collector open-telemetry/opentelemetry-collector \
  --namespace aks \
  --set image.repository="otel/opentelemetry-collector-contrib" \
  --set mode=deployment




**Important Links:**
Links:

[https://opentelemetry.io/docs/collector/installation/ - install otel on Kubernetes](url)

[[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

[https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/examples/kubernetes/otel-collector.yaml#L13](url)

[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/configuration/helm-chart-config/otel-collector/](url)
