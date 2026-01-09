# Deployment Documentation

## Overview
The SMS Spam Detection System is deployed on a Kubernetes cluster with Istio service mesh for traffic management. The system consists of a frontend app-service, a backend model-service, Prometheus for monitoring, and Grafana for visualization.

## Deployment Structure
[Will add a flow to show the high-level architecture showing Vagrant VMs, Kubernetes cluster, Istio components, services, and data flow]

### Components
- **Controller VM**: Runs Kubernetes control plane, NFS server for shared model storage.
- **Worker VMs**: Run application pods.
- **Istio Ingress Gateway**: Handles external traffic at 192.168.56.90.
- **Virtual Services**: Route traffic to app-service versions.
- **Destination Rules**: Ensure consistent version routing.
- **Prometheus**: Scrapes metrics from services.
- **Grafana**: Visualizes metrics and experiment results.

## Data Flow
1. User requests hit Istio Ingress Gateway at project.local.
2. VirtualService routes based on canary rules (90% v1, 10% v2) with sticky sessions.
3. App-service processes request, calls model-service via internal DNS.
4. Model-service uses shared NFS volume for model files.
5. Metrics collected by Prometheus, visualized in Grafana.

## Request Flow
- **Hostname**: project.local
- **Port**: 80/443
- **Path**: / (app), /metrics (Prometheus)
- **Headers**: Cookie for sticky sessions
- **90/10 Split**: Configured in VirtualService weights
- **Routing Decision**: Made by Istio Envoy proxy based on VirtualService rules

## Additional Use Case: Rate Limiting
[To be added once rate limiting PR is merged]

## Experiment Flow
[Will add a flow to show the traffic split, version consistency, metrics collection]