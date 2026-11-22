# Homelab Infrastructure

Declarative Infrastructure-as-Code (IaC) repository for my personal homelab environment. This setup provides container orchestration, reverse proxying, encrypted mesh networking, and real-time hardware/service observability on a single host machine.

## Architecture Overview

The infrastructure is organized into decoupled Docker Compose stacks separated by functional layers, keeping configuration declarative and persistent state isolated from version control.

* **Container Management:** Docker + Portainer CE for lifecycle management, inspection, and stack orchestration.
* **Networking & Reverse Proxy:** Nginx Proxy Manager (NPM) for local domain routing and TLS termination.
* **Private Mesh & Zero-Trust Access:** Tailscale (installed on the host OS) providing WireGuard-based encrypted remote access without exposing public ports on the router.
* **Observability & Monitoring:** Prometheus + Node Exporter + Grafana for real-time host telemetry (CPU, RAM, disk, and thermal metrics).

## Components Used
* **Docker/Docker Compose:** execute each service defined in the YAML file
* **Portainer CE:** Management of the containers
* **Nginx Proxy Manager:** Reverse proxy and access by the net
* **Tailscale** Private Network(VPN)
* **Prometheus:** Inspect the health of the software and hardware.
* **Node Exporter:** Expose the metrics of the Device.
* **Grafana:** Dashboards with the data of the Device

```text
[ Remote Client ]
       │
       ▼ (Encrypted WireGuard Tunnel via Tailscale)
[ Host Machine (Tailscale Node) ]
       │
       ▼ (Ports 80 / 443)
[ Nginx Proxy Manager ] ──► proxy_net (Internal Docker Bridge)
       │
       ├──► Portainer CE      (Container Management)
       ├──► Grafana           (Dashboards & Visualization)
       └──► Prometheus        (Metrics Storage) ◄── Node Exporter (Host Metrics)
```

## Repository Directory
```text
homelab/
├── .gitignore                  # Ignores local secrets (.env) and persistent data
├── README.md                   # Infrastructure documentation
│
├── stacks/                     # Declarative service definitions (Tracked in Git)
│   ├── 01-core/                # Core management services (Portainer CE)
│   │   ├── docker-compose.yml
│   ├── 02-networking/          # Ingress and reverse proxy (Nginx Proxy Manager)
│   │   ├── docker-compose.yml
│   ├── 03-monitoring/          # Observability stack (Prometheus, Node Exporter, Grafana)
│   │   ├── docker-compose.yml
│   └── 04-apps/                # Self-hosted applications and personal projects
│
├── scripts/                    # Maintenance and backup automation scripts
└── data/
```

## Security & Network Model
Zero Public Exposure: No inbound ports are forwarded on the edge router. All remote administrative and service access occurs exclusively over the private Tailscale tailnet (100.x.y.z / MagicDNS).

Network Isolation: Services communicate internally via a shared external Docker bridge network (proxy_net), avoiding unnecessary host port bindings.

Secrets Management: Sensitive credentials and API tokens are injected via local .env files excluded from version control.

### Development
This basic infrastructure is currently under development; some elements, such as the .env.example files, are not yet implemented. The focus is on developing a project to test continuous deployment.

### Author
Alejandro Sánchez Rodríguez
