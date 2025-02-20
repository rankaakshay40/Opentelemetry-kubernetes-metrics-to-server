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

The Configuration for Cluster Role is there 

To bind this to a ServiceAccount, use the following ClusterRoleBinding:

Configuration to ClusterRoleBinding is there

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



--------------------------------------------------------------------------------------------------------------------------------------------

**Install Node Exporter Using Helm**
**Add the Prometheus Community Helm Repo:**
First, add the Prometheus Community Helm repository, which includes the Node Exporter chart:

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

**Install the Node Exporter:**
Use the following command to install the Node Exporter. You can customize the release name and namespace as needed:

helm install node-exporter prometheus-community/prometheus-node-exporter

Verify the Installation:

Check that the Node Exporter pods are running:

kubectl get pods -l app=prometheus-node-exporter

**Check the node-exporter metrics on local via pod forward command**

kubectl get svc --- Get the service name
  
kubectl port-forward svc/node-exporter-prometheus-node-exporter 9100

**For pods**

kubectl port-forward <node-exporter-pod-name> 9100:9100 -n aks

Make Changes in the opentelemtry config file and apply the changes, restart the node-opentelemtry pods


**Important Links:**

[https://opentelemetry.io/docs/collector/installation/ - install otel on Kubernetes](url)

[[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

[https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/examples/kubernetes/otel-collector.yaml#L13](url)

[https://opentelemetry.io/docs/kubernetes/helm/collector/#logs-collection-preset](url)

[https://grafana.com/docs/grafana-cloud/monitor-infrastructure/kubernetes-monitoring/configuration/helm-chart-config/otel-collector/](url)
