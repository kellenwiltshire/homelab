# Homelab Infrastructure

This repository contains the configuration and orchestration manifests for a production-grade self-hosted infrastructure. The architecture is built on **Unraid** using **Docker Compose**, emphasizing high availability, automated lifecycle management, and secure remote access via Cloudflare Tunnels.

---

## Architecture Overview

The deployment follows a modular service-oriented architecture, leveraging a custom external bridge network for container isolation and inter-service communication.

* **Host Environment:** Unraid
* **Orchestration:** Docker Compose
* **Networking:** Dedicated external bridge (`media-centre-network`)
* **Security:** Cloudflare Zero Trust (Tunneling), qBittorrent-VPN routing, and PUID/PGID permission mapping.

---

## Service Inventory

### Content Acquisition & Streaming
A fully automated pipeline for media management and distribution:
* **Plex:** Centralized media server for high-bitrate streaming.
* **Sonarr & Radarr:** Automated PVR for television and cinema, managing metadata and version control.
* **Seerr:** Discovery and request layer for streamlined content acquisition.
* **qBittorrent-VPN:** Containerized download client with mandatory VPN kill-switch integration.

### Automation & Maintenance
Advanced utility services to ensure filesystem integrity and library optimization:
* **Maintainerr:** Automated retention policies and collection management for media libraries.
* **Cleanuparr:** Automated disk space reclamation and library pruning.

### Observability & Infrastructure
Comprehensive monitoring stack for real-time telemetry:
* **Netdata & Glances:** High-resolution system monitoring providing deep visibility into CPU/IO wait, memory pressure, and network throughput.
* **Uptime Kuma:** Service-level monitoring with integrated alerting for infrastructure health.
* **Cloudflared:** Manages the secure tunnel daemon for ingress without traditional port forwarding.

### Miscellaneous
Other services:
* **Mealie:** Self-hosted recipe management engine and API-driven meal planning.
* **Immich:** Self-hosted photo and video backup solution. 

---

## Configuration & Deployment

### Environment Management
The stack is driven by  `.env` files in each directory to maintain a strict separation between configuration and secrets. This ensures portability across different environments and prevents sensitive data leaks.

```bash
# Global UID/GID mapping for filesystem permissions
PUID=1000
PGID=100
TZ=America/Toronto

# Infrastructure credentials (omitted in VCS)
CLOUDFLARE_TUNNEL_TOKEN=...
```

---

### Network Initialization

The services rely on a pre-existing external network to facilitate communication between independent containers while maintaining a flat network topology for the media stack:

```bash
docker network create media-centre-network
```

---

### Development Standards

* **Persistence:** All application data is mapped to persistent volumes within the config/ directory to ensure state is maintained across container lifecycle events.

* **Security:** Services are configured to run with specific PUID/PGID mappings to adhere to the principle of least privilege on the host filesystem.

* **Scalability:** The modular nature of the docker-compose.yml allows for the independent scaling, debugging, or migration of individual service blocks.
