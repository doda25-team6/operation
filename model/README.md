# Model Directory

This directory is mounted as a shared VirtualBox folder to `/mnt/model` in all VMs.

## Purpose

This shared folder is used for:
- Storing ML model files that need to be accessible across all cluster nodes
- Sharing model artifacts between the model-service pods
- Enabling NFS-based persistent storage in Kubernetes

## Usage

The NFS server on the controller node exports `/mnt/model` to all worker nodes, allowing Kubernetes pods to mount this as a persistent volume.

## Kubernetes Integration

See `kubernetes/persistent-volume-nfs.yml` for the PersistentVolume and PersistentVolumeClaim configuration.

## Note

This directory is created automatically by the provisioning process. You can place model files here that should be accessible to all pods in the cluster.
