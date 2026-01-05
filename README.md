# SMS Spam Detection System - Implementation Overview

## Repository Links (Tag: a1)
- **[app](https://github.com/doda25-team6/app/tree/a1)** - Spring Boot frontend and API gateway
- **[model-service](https://github.com/doda25-team6/model-service/tree/a1)** - Flask ML backend with scikit-learn
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a1)** - Version-aware Maven library
- **[operation](https://github.com/doda25-team6/operation/tree/a1)** - Docker Compose orchestration (this repository)

## Repository Links (Tag: a2)
- **[app](https://github.com/doda25-team6/app/tree/a2)**
- **[model-service](https://github.com/doda25-team6/model-service/tree/a2)** 
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a2)**
- **[operation](https://github.com/doda25-team6/operation/tree/a2)**

## Repository Links (Tag: a3)
- **[app](https://github.com/doda25-team6/app/tree/a3)**
- **[model-service](https://github.com/doda25-team6/model-service/tree/a3)** 
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a3)**
- **[operation](https://github.com/doda25-team6/operation/tree/a3)**

## Quick Start

### A1: Docker Compose
```bash
git clone https://github.com/doda25-team6/app.git
git clone https://github.com/doda25-team6/model-service.git
git clone https://github.com/doda25-team6/lib-version.git
git clone https://github.com/doda25-team6/operation.git
cd operation
git checkout a1
docker compose up --build
```

Access the application at http://localhost:8080/sms/

### A2-A4: Kubernetes Cluster

```bash
cd operation
vagrant up

cd ansible && ansible-playbook -u vagrant -i 192.168.56.100, finalization.yml

# Enable Istio sidecar injection for the namespace
kubectl label ns default istio-injection=enabled

# Deploy the application with Prometheus
cd /vagrant/charts/project-app
helm install project .
```

This will:
1. Create 3 VMs (1 controller + 2 workers)
2. Install Kubernetes v1.32.4
3. Configure networking (Flannel CNI)
4. Install cluster services (MetalLB, Nginx Ingress, Dashboard, Istio)

**Access the cluster:**
```bash
# From host machine
kubectl --kubeconfig=admin.conf get nodes

# Or SSH into controller
vagrant ssh ctrl
kubectl get nodes
```

**Destroy cluster:**
```bash
vagrant destroy -f
```

---

## A2: Kubernetes Cluster Setup Details

### IP Allocations

| Service | IP Address | Purpose |
|---------|------------|---------|
| Controller | 192.168.56.100 | Kubernetes API server |
| Worker 1 | 192.168.56.101 | Worker node |
| Worker 2 | 192.168.56.102 | Worker node |
| Nginx Ingress | 192.168.56.90 | HTTP/HTTPS traffic |
| Istio Gateway | 192.168.56.91 | Service mesh traffic |
| MetalLB Pool | 192.168.56.90-99 | LoadBalancer IPs |

### Installed Components

- **Kubernetes**: v1.32.4 (kubeadm, kubelet, kubectl)
- **Container Runtime**: Containerd 1.7.28
- **CNI**: Flannel v0.26.7
- **Load Balancer**: MetalLB v0.15.2
- **Ingress**: Nginx Ingress Controller
- **Dashboard**: Kubernetes Dashboard
- **Service Mesh**: Istio 1.25.2
- **Package Manager**: Helm 3.x
- **Storage**: NFS server on controller

### Accessing Kubernetes Dashboard

**Option 1: Port forwarding**

From your host:
```bash
kubectl --kubeconfig=admin.conf port-forward -n kubernetes-dashboard svc/kubernetes-dashboard-kong-proxy 8443:443 --address 0.0.0.0
```

Then access: https://192.168.56.100:8443

**Option 2: Via hostname**

Add to `/etc/hosts`:
```
192.168.56.91 dashboard.local
```

Access: http://dashboard.local

**Get login token:**
```bash
vagrant ssh ctrl
kubectl -n kubernetes-dashboard create token admin-user
```
### Accessing Prometheus UI

From your host:
```bash
kubectl port-forward --address 0.0.0.0 svc/project-project-app-prometheus 9090:9090
```

Then access: https://192.168.56.100:9090

### Accessing App

Add to `/etc/hosts`:
```
192.168.56.90 project.local
```

Access: http://project.local
        https://project.local/metrics

The application includes Prometheus monitoring with three types of metrics:
- *Counter*: click_rate_total, navigation_requests_total{page}
- *Gauge*: time_on_site_seconds
- *Histogram*: page_load_seconds{page}

### Accessing Grafana

Grafana is automatically deployed with the application for metrics visualization.

**Access via port-forward:**
```bash
kubectl port-forward --address 0.0.0.0 svc/project-project-app-grafana 3000:3000
```

Then access: http://192.168.56.100:3000
- Username: `admin`
- Password: `admin`

**Dashboard Location:**
- Navigate to Dashboards → "Application Metrics"
- Dashboard is automatically provisioned on deployment

**Dashboard Panels (using Prometheus metric names):**
1. **click_rate_total** - Time Series showing request rate (using `rate()` function)
2. **time_on_site_seconds** - Gauge with color thresholds (red <10s, yellow 10-30s, green >30s)
3. **navigation_requests_total** - Histogram showing page visit distribution
4. **page_load_seconds (P95)** - Time Series showing 95th percentile load time (using `histogram_quantile()`)
5. **click_rate_total (Total)** - Stat panel showing total prediction count
6. **page_load_seconds (Statistics)** - Table with avg, P50, P95, P99 by page

**Advanced Features:**
- Uses PromQL functions: `rate()`, `histogram_quantile()`, `sum by()`
- Aggregates metrics across pods where appropriate
- Interactive timeframe selector (5m to 30d)
- Auto-refresh intervals (5s to 5m)

**Manual Dashboard Import (Optional):**

If the dashboard isn't auto-loaded:
1. Copy JSON from `charts/project-app/dashboards/application-metrics.json`
2. In Grafana: Dashboards → Import → Paste JSON
3. Select "Prometheus" as datasource
4. Click Import



### Verification

After `vagrant up` completes, verify all services:

```bash
vagrant ssh ctrl

# All nodes should be Ready
kubectl get nodes

# Check MetalLB
kubectl get pods -n metallb-system

# Check Dashboard
kubectl get pods -n kubernetes-dashboard

# Check Istio
kubectl get pods -n istio-system
istioctl version

# Check LoadBalancer services
kubectl get svc --all-namespaces | grep LoadBalancer
```

### Troubleshooting

**Pods not starting:**
```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
```

**MetalLB not assigning IPs:**
```bash
kubectl describe ipaddresspool -n metallb-system
kubectl logs -n metallb-system -l app=metallb
```

**Istio issues:**
```bash
istioctl analyze
kubectl get pods -n istio-system
```

### Configuration

**Change worker count:**
```bash
WORKER_COUNT=3 vagrant up
```

**Adjust resources:**
```bash
CTRL_CPUS=4 CTRL_MEMORY=8192 vagrant up
```

---

## Feature Implementation Details

## F1: Version-aware Library

A version-aware Maven library `lib-version` has been created in the [lib-version repository](https://github.com/doda25-team6/lib-version/tree/a1). This library contains a `VersionUtil` class that provides version information parsed from a `version.properties` resource file included in the package.

### Implementation Details
- **Version Storage**: Version information stored in `src/main/resources/version.properties`
- **Integration**: The `app` service depends on this library and uses `VersionUtil` during startup
- **Usage**: Version information is logged when the application starts, providing runtime version awareness

## F2: Library Release

The `lib-version` library has automated release workflows implemented via GitHub Actions. The library is automatically packaged, versioned, and released to the GitHub Package Registry for Maven.

### Workflow Details
- **Automation**: GitHub Actions workflow in `.github/workflows/release.yml`
- **Trigger**: Git version tags (e.g., `v1.0.0`) trigger the release workflow
- **Registry**: Published to GitHub Package Registry for Maven
- **Versioning**: Automatic version determination from git tags
- **Packaging**: Maven packaging with proper dependency management

### Usage
The released library can be consumed by adding the GitHub Package Registry as a Maven repository and including the dependency:

```xml
<dependency>
    <groupId>team6</groupId>
    <artifactId>lib-version</artifactId>
    <version>1.0.0</version>
</dependency>
```

## F3

Navigate to `operation` and start up the containers for model-service and app by running the following commands:
`docker compose build`
`docker compose up`
(Ensure Docker is up and running)

The service can be accessed on:
http://localhost:8080

To stop the containers: `docker compose down`

## F4

To satisfy F4, GitHub Actions workflows were added to both model-service and app repositories `(.github/workflows/release-image.yml)`
These workflows automatically build Docker images and publish them to GitHub Container Registry (GHCR) whenever a version tag is pushed.

## Triggering the workflow
A new version tag was created and pushed:
```
git tag v0.1.1
git push origin v0.1.1
```
This triggers the `release-image.yml` workflow, which can be observed in each repository's Actions tab.

### Pulling the Published Images
After the workflow completes successfully, the generated images become available on GHCR and can be pulled locally:
Model Service: `docker pull ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker pull ghcr.io/doda25-team6/app:v0.1.1`

### Testing the Images
To verify that the published images run correctly, they were executed locally:
Model Service: `docker run -p 8081:8081 ghcr.io/doda25-team6/model-service:v0.1.1`
App: `docker run -p 8080:8080 ghcr.io/doda25-team6/app:v0.1.1`

## F6

To satisfy F6, environment variables were added into the Dockerfiles for `model-service` and `app`. Below are given the environment variables that can be modified in `operation/docker-compose.yml` or when running the components individually:

### Environment variables in `app`

- `SERVER_PORT`: The port on which the app runs within the container (default: 8080)
- `MODEL_HOST`: The URL on which model service runs for the app to use, relative to the app within the container (default: http://model-service:8081)

### Environment variable in `model-service`

- `SERVER_PORT`: The port on which the service runs within the container (default: 8081)

## F9

In the `model-service` repository, a dedicated workflow was created, which can be run on demand. 

The workflow is triggered by navigating to the Actions tab in the GitHub web UI, clicking "On-demand training, versioning, and release of the model" on the left, and pressing "Run workflow." You will be asked for a release tag, which represents a semantic version, e.g., v0.0.1. Note that existing tags cause the workflow to fail its run.

Afterward, the workflow will run and create a release at https://github.com/doda25-team6/model-service/releases. The release contains corresponding versioned model files, which can be downloaded without authentication.

The workflow consists of two jobs, namely `train-and-version` and `release`. This design decision was made so that training and versioning could be performed inside the same Docker Python container, which avoids user permission issues. Releasing the model files is not done inside this container, but in the GitHub runner. That is why, two jobs are used, where the output files from the first job are downloaded to the second (dependent) job.

## F10

A few changes have been made to remove the hard-coded use of the model by the model-service container. Firstly, the Docker Compose file in `operation` was extended with an environement variable and a volume mount. Secondly, the Dockerfile and `serve_model.py` were changed in `model-service`.

The Dockerfile does not have an environment variable for the model file location. This is defined in the Docker Compose file only, to simplify the configuration.

In `serve_model.py`, logic has been added to check whether the model can be loaded in the container, or if it has to be downloaded from the GitHub releases page in the `model-service` repository. It has been chosen to use a global variable `clf` in `serve_model.py`, instead of using Flask's app config, as it's expected to work well given the relatively small scale of the project.
