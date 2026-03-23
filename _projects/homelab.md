---
layout: page
title: self hosted k3s
description: Self hosted k3s cluster
img: assets/img/Homelab-without-services.webp
importance: 1
category: Self Hosting
related_publications: false
---

# Homelab Kubernetes (K3s) Infrastructure Overview

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/Homelab-without-services.webp" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

---

This homelab is a **production-grade Kubernetes (K3s) cluster** designed to replicate real-world cloud and platform engineering patterns on affordable hardware.
It focuses on **high availability, security, observability, storage resilience, and network segmentation**, while remaining fully self-hosted.

The cluster is used to run both **publicly exposed services** and **private internal applications**, protected by VPN and security tooling.

---

## 1. Compute Layer

### Kubernetes Nodes

The cluster consists of **multiple nodes with mixed architectures**, providing realistic operational constraints:

- **4 × Raspberry Pi nodes**

  - Used as Kubernetes control plane and worker nodes
  - Each node runs a **CrowdSec firewall bouncer** for node-level protection

- **1 × HP ProDesk 405 G4 (Mini PC)**
  - Used as Kubernetes control plane
  - Higher-performance node for workloads requiring more CPU/RAM
  - Also runs a CrowdSec firewall bouncer

---

## 2. Kubernetes Distribution

- **[K3s](https://k3s.io/)** is used as the Kubernetes distribution
- Cluster is deployed in **HA mode** (multiple control-plane nodes)

---

## 3. Networking & Traffic Management

### Ingress & Load Balancing

- **[Traefik](https://doc.traefik.io/traefik/)** is used as the Kubernetes Ingress Controller

  - Handles HTTP/HTTPS routing
  - Separates **public applications** from **internal-only services**

- **[MetalLB](https://metallb.io/)** provides bare-metal LoadBalancer functionality
  - Assigns stable IP addresses to services
  - Supports real client IP preservation

### Network Segmentation

- **Public apps**

  - Accessible from the internet via Traefik

- **Internal apps**
  - Only accessible inside the cluster or via VPN

### VPN Access

- **[WireGuard](https://www.wireguard.com/)** provides secure remote access to internal services
- Allows safe administration and private app access without exposing them publicly

### Kube-VIP

- **[Kube-VIP](https://kube-vip.io/)** provides a load balancer for the control plane.

---

## 4. Security Stack

Security is implemented at **multiple layers**:

### CrowdSec

- **[CrowdSec agent](https://www.crowdsec.net/)** runs on each node as a daemonset

  - Monitors logs and system metrics for suspicious activity
  - Detects brute-force attacks, port scans, and other threats

- **[Firewall bouncer](https://docs.crowdsec.net/u/bouncers/firewall/) installed on each node**

  - Enforces bans at the OS firewall level
  - Protects both Kubernetes services and node-level access

- CrowdSec decisions are shared across the infrastructure
- [Crowdsec WAF](https://docs.crowdsec.net/docs/next/appsec/intro) is used to protect public-facing services

### Security & Upgrade

- Secrets are encrypted at rest using `aescbc` provider.
- Automatic cluster upgrades via [Rancher System Upgrade Controller](https://github.com/rancher/system-upgrade-controller)

---

## 5. Observability & Monitoring

A full **monitoring stack** is deployed inside Kubernetes:

- **Prometheus Operator**

  - Metrics collection

- **Grafana**

  - Dashboards and visualization

- **Alertmanager**
  - Alert routing and notifications

This provides visibility into:

- Node health
- Pod performance
- Resource usage
- Application metrics

---

## 6. Storage Architecture

### Local & Network Storage

The infrastructure uses **multiple storage backends**, depending on workload needs:

- **2 SATA-SSD**

  - Available on two different hosts
  - Used for Kubernetes persistent volumes
  - Provided via Longhorn

- **2 HDD**

  - Used for Immich data
  - Managed with Longhorn for replication and snapshots

- **External HDD**

  - Used for media storage

- **External HDD**
  - Backup of the media storage

### Backups

- **Object storage (S3-compatible)** hosted externally

  - Automated backups of databases

  - Enables disaster recovery and off-site redundancy

---

## 7. Data & Supporting Services

Inside the cluster, several shared services are deployed:

- **Redis**

  - Caching and background job support

- **RabbitMQ**

  - Asynchronous messaging

- **Pi-hole**

  - Internal DNS filtering

- **CrowdSec API**

  - Central decision engine for security enforcement

- **PostgreSQL**
  - Application databases
  - Installed directly on OS

---

## 8. Infrastructure management

- Hosts managed via **Ansible** for configuration consistency
- Kubernetes manifests stored in **GitHub repository**
- **GitHub Actions** used for CI/CD deployments
- Self-hosted GitHub runners deployed in the cluster for secure deployments
- External resources and record DNS managed with **Terraform**

---

## History

The homelab started in 2020 by self-hosting Nextcloud on a single Raspberry Pi and then evolved through a multi-node Docker deployment before migrating to k3s in 2024.

| Date          | Event                                                                                                            |
| ------------- | ---------------------------------------------------------------------------------------------------------------- |
| March 2026    | Replace the NFS storage by Longhorn.                                                                             |
|               | Remove BTRFS RAID 1 array.                                                                                       |
| February 2026 | Add HP ProDesk 405 G4 Mini.                                                                                      |
|               | Remove the media-server RAID 1 array and migrate to a primary/backup layout.                                     |
| January 2026  | Add Eaton UPS.                                                                                                   |
| October 2025  | Added a 5th Node (Intel NUC) to the cluster to improve control plane HA and workload capacity.                   |
| May 2024      | Add a RaspberryPi 5B+ (8GB) to the setup. Create the kubernetes cluster. Migrate apps from Docker to Kubernetes. |
| June 2023     | Add a RaspberryPi 4B (4GB) to host internal services.                                                            |
| December 2022 | Add a RaspberryPi 4B (8GB) to serve as a media server.                                                           |
| February 2020 | Start of the homelab by self hosting Nextcloud on a RaspberryPi 4B+                                              |

---
