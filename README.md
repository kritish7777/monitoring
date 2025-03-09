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
* helm install loki grafana/loki-stack -f values.yaml -n prod-monitoring (using custom values.yaml file)
* kubectl get pods -n prod-monitoring

### What is Promtail?

Promtail is an agent that collects logs from Kubernetes pods and sends them to Loki for storage and querying. It works similarly to Fluentd or Logstash, but is specifically designed to work with Loki.

### Why Do We Need Promtail?

Loki only stores logs, but it does not collect them. Promtail solves this by:

1. Reading logs from container runtimes (Docker, containerd, etc.).

2. Labeling logs (adding metadata like pod name, namespace, etc.).

3. Forwarding logs to Loki for centralized logging and analysis.

### How Promtail Works in Kubernetes
1. Runs as a DaemonSet (one Promtail pod per node).

2. **Tails logs from /var/log/** (where Kubernetes stores container logs).

3. Uses Kubernetes API to enrich logs with pod/namespace labels.

4. Pushes logs to Loki for indexing.

# Understand promtail.yaml

# Deamonset.yaml

![Screenshot 2025-03-07 225035](https://github.com/user-attachments/assets/c3516cce-1eb7-4d5f-9250-5086eb93bc0d)

1.  **/var/log** → Mounted for system logs
   * This allows Promtail to read logs stored in /var/log on the node.

2.  **/etc/promtail** → Mounted for Promtail config files
    * This is where Promtail stores its configuration files
    * Allows Promtail to load its configuration dynamically.
  
 3. /var/lib/docker/containers → Mounted for container logs
    * This is important since Docker keeps container logs here.
    * Read-only mode makes Promtail only able to read logs, but not modify them.

# configmap.yaml

![Screenshot 2025-03-07 231335](https://github.com/user-attachments/assets/a8096a5c-33eb-4d03-9cc8-56a259721504)

1. Clients (Where Logs Are Sent)
   * This defines where Promtail sends logs (Loki endpoint).
     
2. Scrape Configs (How Logs Are Collected)
   * Scrapes logs from all Kubernetes pods.
   * role: pod → Automatically discovers pods running in the cluster.





