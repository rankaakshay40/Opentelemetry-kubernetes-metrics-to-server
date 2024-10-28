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

**This will get us the name of the cluster in aws**

aws eks list-clusters

**Verify installation with the command:**
helm list -n aks

**Verify the pods We will see a pod for kube-state metrics running and a pod for otel-collector agent running.**

kubectl get pods -n [NAMESPACE]

**Now Get the configuration for the otel-collector in a file with this command**

kubectl get configmap otel-collector-conf -o yaml > otel-collector-conf.yaml

**Edit the otel-collector-conf.yaml**

This configuration adds four scrape targets with specific functions for discovery and scraping:

All Nodes, scraping their cAdvisor endpoint (integrations/kubernetes/cadvisor)
All Nodes, scraping their Kubelet metrics endpoint (integrations/kubernetes/kubelet)
All Pods with the app.kubernetes.io/name=kube-state-metrics label, scraping their /metrics endpoint (integrations/kubernetes/kube-state-metrics)
All Pods with the app.kubernetes.io/name=prometheus-node-exporter.* label, scraping their /metrics endpoint (integrations/node_exporter)


Set up RBAC for OpenTelemetry Metrics Collector
This configuration uses the built-in Kubernetes service discovery, so you must set up the service account running the OpenTelemetry Collector with advanced permissions (compared to the default set). The following ClusterRole provides a good starting point.

The Configuration for Cluster Role is[ https://github.com/rankaakshay40/Opentelemetry-xmon-kubernetes/blob/main/ClusterRole.yaml](url)

To bind this to a ServiceAccount, use the following ClusterRoleBinding:

The Configuration to ClusterRoleBinding is here: [https://github.com/rankaakshay40/Opentelemetry-xmon-kubernetes/blob/main/CluserRoleBinding.yaml](url)

**Now apply the configuration changes**

kubectl apply -f otel-collector-conf.yaml
kubectl apply -f ClusterRole.yaml
kubectl apply -f ClusterRoleBinding.yaml

We can also use 

kubectl apply -f otel-collector-conf.yaml --force in some cases

**Now to get the changes reflect, we need to restart the otel-collector pod.**

kubectl delete pod <otel-collector pod name>

**Now Check the logs for the pods to see if we are getting any error**

kubectl logs <pod-name>

**Check the Prometheus and Grafana Endpoint and see the metrics at port 9090 and 3000**


**Important Links:**
Links:

[https://opentelemetry.io/docs/collector/installation/ - install otel on Kubernetes](url)

[[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

[https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/examples/kubernetes/otel-collector.yaml#L13](url)

[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/configuration/helm-chart-config/otel-collector/](url)
