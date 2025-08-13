# NVIDIA DCGM Exporter Ansible Role

This repository contains an Ansible role for installing and configuring the NVIDIA DCGM Exporter on systems with NVIDIA GPUs.

## Overview

The NVIDIA DCGM Exporter is a Prometheus exporter for GPU metrics that provides detailed monitoring capabilities for NVIDIA GPUs. This Ansible role automates the installation and configuration process.

## Features

- ✅ Installs NVIDIA Management Library RPM package
- ✅ Installs NVIDIA Container Toolkit RPM package
- ✅ Updates Container Device Interface (CDI) configuration
- ✅ Creates and manages systemd service
- ✅ Runs DCGM Exporter in a container using podman
- ✅ Configurable container image tag and RPM version
- ✅ Automatic service management (start, enable, restart)

## Quick Start

1. **Clone this repository:**

   ```bash
   git clone <repository-url>
   cd dcgm-exporter-ansible
   ```

2. **Update the inventory file** (`inventory.yml`) with your GPU server details.

3. **Run the playbook:**

   ```bash
   ansible-playbook -i inventory.yml playbook.yml
   ```

## Prerequisites

- Ansible 2.18 or higher
- Target systems must have:
  - Podman installed
  - NVIDIA drivers configured
  - Network access to pull container images

## Verification

After installation, you can verify the service is running:

```bash
# Check service status
systemctl status dcgm-exporter

# Check if metrics are available
curl http://localhost:9400/metrics

# Check container status
podman ps | grep dcgm-exporter
```
