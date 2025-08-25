# NVIDIA DCGM Exporter Ansible Role

This Ansible role installs and configures the NVIDIA DCGM Exporter for GPU monitoring and metrics collection.

## Requirements

- Ansible 2.18 or higher
- Podman installed on the target system

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `dcgm_exporter_nvidia_repo_url` | `"https://developer.download.nvidia.com/compute/cuda/repos/rhel9/x86_64/"` | Base URL for RPM downloads |
| `dcgm_exporter_image_tag` | `"4.3.1-4.4.0-ubi9"` | Container image tag for the DCGM Exporter |
| `dcgm_exporter_libnvidia_ml_version_release_fallback` | `"575.57.08-1.el9"` | Fallback version for NVIDIA Management Library RPM if nvidia-driver is not installed |
| `dcgm_exporter_libnvidia_ml_version_release_force` | `"560.28.03-1.el9"` | (Optional) If set, forces installation of this specific libnvidia-ml RPM version, overriding auto-detection |
| `dcgm_exporter_libnvidia_container_toolkit_version_release` | `"1.17.8-1"` | Version of the NVIDIA Container Tools RPM to install |

## Dependencies

None.

## Example Playbook

```yaml
---
- hosts: gpu_servers
  roles:
    - nvidia-dcgm-exporter
```

With custom image tag:

```yaml
---
- hosts: gpu_servers
  vars:
    dcgm_exporter_image_tag: "4.3.1-4.4.0-ubi9"
  roles:
    - nvidia-dcgm-exporter
```

## What This Role Does

1. **Installs NVIDIA Management Library**: Automatically detects the installed nvidia-driver version and installs the matching libnvidia-ml RPM. Falls back to the configured version if nvidia-driver is not found.
2. **Installs NVIDIA Container Tools**: Installs the libnvidia-container-tools RPM for container GPU support.
3. **Updates CDI Configuration**: Adds the required mount configuration to `/etc/cdi/nvidia.yaml` for nvidia-ml library access
4. **Creates Systemd Service**: Creates a systemd service file that manages the DCGM Exporter container
5. **Manages Service**: Enables and starts the DCGM Exporter service

## Container Configuration

The role runs the DCGM Exporter in a container with the following configuration:

- **Image**: `nvcr.io/nvidia/k8s/dcgm-exporter:{{ dcgm_exporter_image_tag }}`
- **Port**: 9400 (Prometheus metrics endpoint)
- **Devices**: All NVIDIA GPUs (`nvidia.com/gpu=all`)
- **Capabilities**: SYS_ADMIN
- **Environment**: DCGM_EXPORTER_DEBUG=true

## Service Management

The role creates a systemd service that:

- Automatically starts on boot
- Restarts on failure
- Uses podman to manage the container lifecycle

## Metrics Endpoint

Once deployed, metrics will be available at `http://your-server:9400/metrics`
