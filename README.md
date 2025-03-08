# What is Grafana?

Grafana is an open-source tool for observability and monitoring. It enables you to query, visualize, and alert on logs and metrics from several sources of data such as Prometheus.
# How does Grafana work?

Grafana operates by connecting to data sources, querying the data, and visualizing it in the form of dashboards. Grafana supports a variety of visualization options such as graphs, tables, heatmaps, etc. Grafana also has the ability to send alerts based on certain conditions.

# Understand deployment.yaml file

<img width="302" alt="image" src="https://github.com/user-attachments/assets/46358a95-d771-4e12-9c06-4231f74765e9" />

### /var/lib/grafana:
* This is the directory where Grafana stores its data (e.g., dashboards, plugins, and SQLite database).
* The volume grafana-storage is mounted here to ensure that Grafana's data persists even if the Pod is restarted or rescheduled.


### /usr/share/grafana/conf:
* This is the directory where Grafana's configuration files are stored.
* The volume grafana-config is mounted here to provide Grafana with its configuration (defaults.ini).

<img width="497" alt="image" src="https://github.com/user-attachments/assets/0ded76e0-bf85-4750-970f-d5b1291b4170" />


### persistentVolumeClaim
* Specifies that the volume is backed by a PersistentVolumeClaim (PVC).

### claimName
* The name of the PVC to use.

### configMap
* Specifies that the volume is backed by a ConfigMap.

## Service
we use type-loadbalancer.This exposes the Service externally through a cloud provider's load balancer (e.g., AWS ELB, Azure Load Balancer, GCP Load Balancer). It gives the Service an external IP or hostname.

### Loki

Loki is a log storage and querying solution developed by Grafana Labs. It efficiently collects, stores, and searches logs, particularly for cloud native applications such as Kubernetes. Loki does not capture logs. Instead, it uses Promtail, Fluent Bit, or other log agents to gather logs from apps and forward them to Loki.

### Loki Installation

We install loki using helm:

# Add the Loki Helm Repository
* helm repo add grafana https://grafana.github.io/helm-charts
* helm repo update





