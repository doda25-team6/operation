# SMS Spam Detection System - Implementation Overview

This repository contains the operational setup for the SMS Spam Detection system, covering assignments A1 through A4 with complete deployment instructions.

## Repository Links

- **[app](https://github.com/doda25-team6/app)** - Spring Boot frontend and API gateway
- **[model-service](https://github.com/doda25-team6/model-service)** - Flask ML backend with scikit-learn
- **[lib-version](https://github.com/doda25-team6/lib-version)** - Version-aware Maven library
- **[operation](https://github.com/doda25-team6/operation)** - Docker Compose orchestration (this repository)

## Repository Links (Tag: a1)
- **[app](https://github.com/doda25-team6/app/tree/a1)**
- **[model-service](https://github.com/doda25-team6/model-service/tree/a1)**
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a1)**
- **[operation](https://github.com/doda25-team6/operation/tree/a1)** 

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

## Repository Links (Tag: a4)
- **[app](https://github.com/doda25-team6/app/tree/a4)**
- **[model-service](https://github.com/doda25-team6/model-service/tree/a4)** 
- **[lib-version](https://github.com/doda25-team6/lib-version/tree/a4)**
- **[operation](https://github.com/doda25-team6/operation/tree/a4)**

## Prerequisistes

### Required Tools

1. **Git**: Version control system
```bash
# Ubuntu/Debian
sudo apt update && sudo apt install git

# macOS
brew install git
```

2. **Docker & Docker Compose**: Container runtime
```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
sudo apt install docker-compose-plugin
# Restart session or run: newgrp docker
```

3. **Java 25 & Maven**: For building Java applications
```bash
sudo apt install openjdk-25-jdk maven
```

4. **Python 3**: For machine learning service
```bash
sudo apt install python3 python3-pip python3-flask python3-scikit-learn python3-joblib
```

5. **Vagrant & VirtualBox**: For Kubernetes cluster provisioning (A2)
```bash
sudo apt install virtualbox vagrant
```

6. **kubectl & Helm**: Kubernetes tools (A2-A4)
```bash
# kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/

# Helm
curl https://get.helm.sh/helm-v3.14.0-linux-amd64.tar.gz -o helm.tar.gz
tar -zxvf helm.tar.gz && sudo mv linux-amd64/helm /usr/local/bin/
```

### Environment Variables
Set these environment variables for GitHub Package Registry access:
```bash
export GITHUB_USERNAME="your-github-username"
export GITHUB_TOKEN="your-github-personal-access-token"
```

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

## A1: Containerization

This assignment demonstrates containerization of the SMS Spam Detection system using Docker Compose.

### Step 1: Build lib-version Library
```bash
# Set environment variables for GitHub access
export GITHUB_USERNAME="your-username"
export GITHUB_TOKEN="your-token"

# Build the shared library
cd lib-version
mvn clean compile
mvn test
cd ..
```

### Step 2: Build and Start Services
```bash
# Navigate to operation directory
cd operation

# Build and start all services
docker compose up --build
```

### Step 3: Verify Deployment
```bash
# Check running containers
docker compose ps

# View service logs
docker compose logs -f app
docker compose logs -f model-service

# Test the API
curl -X POST -H "Content-Type: application/json" \
  -d '{"sms": "Hello world"}' \
  http://localhost:8080/sms/
```

### Step 4: Access Application
- **Web Interface**: http://localhost:8080/sms/
- **API Endpoint**: POST to http://localhost:8080/sms/ with JSON body `{"sms": "your message"}`

### Step 5: Stop Services
```bash
docker compose down
```

## A2: Kubernetes Cluster Provisioning

### Kubernetes Cluster Setup Details

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


### Step 1: Provision Cluster
```bash
# Navigate to operation directory
cd operation

# Start Kubernetes cluster (takes 10-15 minutes)
vagrant up
```

This creates:
- 1 control plane node (192.168.56.100)
- 2 worker nodes (192.168.56.101, 192.168.56.102)

### Step 2: Configure Cluster Services
```bash
# Run finalization playbook
cd ansible
ansible-playbook -u vagrant -i 192.168.56.100, finalization.yml
cd ..
```

### Step 3: Configure kubectl
```bash
# Set kubectl context
export KUBECONFIG=$(pwd)/admin.conf

# Verify cluster
kubectl get nodes
kubectl get pods -A
```

### Step 4: Verify Installation
After `vagrant up` completes, verify all services:

```bash
vagrant ssh ctrl

# All nodes should be Ready
kubectl get nodes
```
```bash
# Check cluster components
kubectl get pods -n metallb-system # Load balancer
kubectl get pods -n ingress-nginx # Ingress controller
kubectl get pods -n istio-system # Service mesh
istioctl version
kubectl get pods -n kubernetes-dashboard # Dashboard
kubectl get svc --all-namespaces | grep LoadBalancer # Check Loadbalancer services
```

### Accessing Kubernetes Dashboard

**Option 1: Port forwarding**

From ctrl:
```bash
kubectl port-forward -n kubernetes-dashboard svc/kubernetes-dashboard-kong-proxy 8443:443 --address 0.0.0.0
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

## A3: Monitoring and Operations

This assignment deploys monitoring and operational tools using Helm charts.

### Step 1: Enable Istio Injection
```bash
# Enable sidecar injection for default namespace
kubectl label ns default istio-injection=enabled
```

### Step 2: Deploy Application with Monitoring
```bash
# Deploy using Helm chart
cd charts/project-app
helm install project .
```

### Step 3: Access Monitoring Stack
```bash
# Get service IPs
kubectl get svc -A

# Access URLs (replace with actual IPs from above):
# Grafana: http://192.168.56.90/grafana/
# Prometheus: http://192.168.56.90/prometheus/
# Kubernetes Dashboard: http://192.168.56.90/kubernetes-dashboard/
```

### Accessing Prometheus UI

From ctrl:
```bash
kubectl port-forward --address 0.0.0.0 svc/project-project-app-prometheus 9090:9090
```

Then access: http://192.168.56.100:9090

### Accessing AlertManager UI

From ctrl:
```bash
kubectl port-forward --address 0.0.0.0 svc/project-project-app-alertmanager 9093:9093
```

Then access: http://192.168.56.100:9093

AlertManager displays Prometheus alerts. To test, generate high traffic and alerts will appear when request rate exceeds 15/min for 2 minutes.

**To configure email alerts:**
1. Get Gmail App Password: https://myaccount.google.com/apppasswords (requires 2FA)
2. Create Secret: `kubectl create secret generic alertmanager-smtp --from-literal=smtp-password='YOUR_APP_PASSWORD'`
3. Deploy: `helm upgrade project . --set alertmanager.email.to=your@gmail.com --set alertmanager.email.from=your@gmail.com`

### Accessing App

Add to `/etc/hosts`:
```
192.168.56.90 project.local
```

Access: http://project.local
        http://project.local/metrics

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


### Step 4: Verify Monitoring
```bash
# Check application pods
kubectl get pods

# Check monitoring pods
kubectl get pods -n monitoring
kubectl get pods -n istio-system
```

## A4: Service Mesh

This assignment demonstrates Istio service mesh capabilities including traffic management and observability.

### Step 1: Verify Istio Installation
```bash
# Check Istio components
kubectl get pods -n istio-system

# Verify sidecar injection
kubectl get pods -o jsonpath='{.items[*].spec.containers[*].name}'
```

### Step 2: Configure Traffic Management
```bash
# Apply Istio configurations
kubectl apply -f charts/project-app/istio-app-dr.yaml
kubectl apply -f charts/project-app/istio-app-vs.yaml
kubectl apply -f charts/project-app/istio-gateway.yaml
```

### Step 3: Test Service Mesh Features
```bash
# Check virtual services
kubectl get virtualservice -n default

# Check destination rules
kubectl get destinationrule -n default

# Check gateway
kubectl get gateway -n default
```

### Step 4: Access Application via Istio
- **Istio Gateway**: http://192.168.56.91/sms/
- **Ingress**: http://192.168.56.90/sms/

#### Port Forwarding for Istio Gateway
```bash
# Access Istio Gateway directly
kubectl port-forward --address 0.0.0.0 svc/istio-gateway 8080:80 -n istio-system
# Then access: http://localhost:8080/sms/
```



## Troubleshooting

### Common Issues

#### Docker Issues
- **Permission denied**: Add user to docker group: `sudo usermod -aG docker $USER`
- **Port already in use**: Change ports in `docker-compose.yml`
- **Build fails**: Ensure Docker daemon is running

#### Kubernetes Issues
- **VT-x not available**: Enable virtualization in BIOS
- **Vagrant fails**: Run `vagrant destroy -f` then `vagrant up`
- **Ansible fails**: Check SSH connectivity to VMs
- **Pods not starting:**
```bash
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace>
```
- **MetalLB not assigning IPs:**
```bash
kubectl describe ipaddresspool -n metallb-system
kubectl logs -n metallb-system -l app=metallb
```
- **Istio issues:**
```bash
istioctl analyze
kubectl get pods -n istio-system
```

#### General Issues
- **GitHub authentication**: Set `GITHUB_USERNAME` and `GITHUB_TOKEN`
- **Out of disk space**: Clean up with `docker system prune -a`
- **Network issues**: Check firewall settings



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

### Useful Commands

```bash
# Docker cleanup
docker compose down --remove-orphans
docker system prune -a

# Kubernetes cleanup
kubectl delete ns project
helm uninstall project

# Vagrant cleanup
vagrant destroy -f
```

<!-- ## Feature Implementation Details

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

In `serve_model.py`, logic has been added to check whether the model can be loaded in the container, or if it has to be downloaded from the GitHub releases page in the `model-service` repository. It has been chosen to use a global variable `clf` in `serve_model.py`, instead of using Flask's app config, as it's expected to work well given the relatively small scale of the project. -->
