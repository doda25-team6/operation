# Model Directory

This directory is a VirtualBox shared folder mounted in VMs for development/testing convenience.

## Purpose

This local directory can be used for:
- Quick access to model files during development
- Testing model file placement before deployment

## Kubernetes Persistent Storage

**Important:** For Kubernetes pods, model storage uses **`/srv/nfs/model`** on the controller node, NOT `/mnt/model`.

- `/mnt/model` is a VirtualBox shared folder (vboxsf filesystem) that **cannot be exported via NFS**
- `/srv/nfs/model` is a native directory created by Ansible that NFS can properly export
- The NFS server on the controller exports `/srv/nfs/model` to all worker nodes

## Kubernetes Integration

See `kubernetes/persistent-volume-nfs.yml` for the PersistentVolume and PersistentVolumeClaim configuration.

## Note

- The `/srv/nfs/model` directory is created automatically by Ansible provisioning
- To add model files for pod access, place them in `/srv/nfs/model` on the ctrl VM
- This VirtualBox shared `./model` directory is separate from the Kubernetes NFS storage
