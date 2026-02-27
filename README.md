<img width="1920" height="1080" alt="Black and Green Modern Marketing Plan Presentation" src="https://github.com/user-attachments/assets/42d081c8-ff7b-43fc-b9f7-342275ed8c4a" /># Envoy Gateway on K3s/K8s with MetalLB

<img width="1920" height="1080" alt="Black and Green Modern Marketing Plan Presentation" src="https://github.com/user-attachments/assets/c7d3e9bd-b81e-48a0-b8ec-b68a5100a43f" />


A step-by-step guide to deploy [Envoy Gateway](https://gateway.envoyproxy.io/) on a bare-metal K3s cluster using MetalLB as the LoadBalancer provider.

## Overview

This repository contains manifests and setup guides for running Envoy Gateway with the Kubernetes Gateway API on an on-premise K3s cluster. MetalLB handles LoadBalancer IP assignment, and Envoy Gateway manages HTTP/HTTPS traffic routing.

### Architecture

```bash
Client Request
       │
       ▼
   MetalLB (L2/BGP)
   Assigns External IP
       │
       ▼
   Envoy Gateway (envoy-gateway-system namespace)
   ├── envoy-gateway (controller)
   └── envoy-proxy (data plane)
       │
       ▼
   HTTPRoute
   (routes traffic based on hostname/path)
       │
       ▼
   Backend Services (any namespace)
```

## Prerequisites

- K3s/K8S cluster (single or multi-node)
- `kubectl` configured
- Helm v3 installed
- A TLS certificate stored as a Kubernetes Secret (for HTTPS)

## Repository Structure

| File | Description |
| --- | --- |
| `01-metallb-2-setup.md` | MetalLB installation and IP pool configuration |
| `02-envoy-gateway-setup.md` | Envoy Gateway installation via Helm |
| `03-gateway.yaml` | Gateway resource with HTTP and HTTPS listeners |
| `04-gatewayclass.yaml` | GatewayClass resource for Envoy Gateway |
