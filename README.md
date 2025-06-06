# CryptoBalancer Infrastructure

[![Kubernetes](https://img.shields.io/badge/kubernetes-v1.25+-green)](https://kubernetes.io/)
[![Traefik](https://img.shields.io/badge/ingress-traefik-orange)](https://traefik.io/)
[![cert-manager](https://img.shields.io/badge/certificates-cert--manager-blueviolet)](https://cert-manager.io/)

---

## Overview

This repository contains the Kubernetes infrastructure configuration for the CryptoBalancer application. It now uses [Helm](https://helm.sh/) for modular, reusable, and industry-standard deployment of all components.

---

## Repository Structure

```
cryptobalancer-infra/
  helm/
    backend/      # Helm chart for backend
    frontend/     # Helm chart for frontend
    cert-manager/ # Helm chart for cert-manager
  base/           # Legacy manifests (to be deprecated)
```

---

## Infrastructure Design

### Namespace & Isolation

- All components are deployed in the `cryptobalancer` namespace for logical separation and access control.

### Components

- **Backend Service**
  - 2 replicas for high availability
  - Backend API on port 8080
  - Resources: 100m CPU/256Mi (requests), 500m CPU/512Mi (limits)
  - Security: Non-root, capability dropping, privilege escalation prevention
- **Frontend Service**
  - 2 replicas for high availability
  - NGINX web interface on port 80
  - Resources: 50m CPU/128Mi (requests), 200m CPU/256Mi (limits)
  - Security: Non-root (nginx user 101), capability dropping, privilege escalation prevention
- **Networking**
  - Services: ClusterIP for internal routing
  - Ingress: Traefik-based routing with automatic HTTPS
    - Frontend: `cryptobalancer.eu`
    - Backend: `api.cryptobalancer.eu`
- **TLS Configuration**
  - cert-manager: Automatic certificate provisioning/renewal
  - Let's Encrypt: Production ACME server
  - Challenge: HTTP-01 with Traefik solver
- **Update Strategy**
  - RollingUpdate: `maxSurge: 1`, `maxUnavailable: 0` (zero downtime)

### Security Measures

1. **TLS Everywhere**: Automatic HTTPS with Let's Encrypt
2. **Container Security Context**: Non-root, drop ALL capabilities, no privilege escalation
3. **Security Headers**: X-Frame-Options, X-Content-Type-Options, X-XSS-Protection, Referrer-Policy, Content-Security-Policy
4. **HTTPS Enforcement**: SSL redirect, force SSL redirect
5. **Request Limiting**: 10MB max request size

---

## Configuration Details

- **Backend Image**: `ghcr.io/xlillium/test-gh-deploy-backend:a94a8977771d8c31680f317e9a8d141264dd6aa3`
- **Frontend Image**: `ghcr.io/xlillium/test-gh-deploy-frontend:1589cd8aef1a5d320b3192d0689f8a53154bde12`
- **Exposed Services**:
  - Frontend: https://cryptobalancer.eu
  - Backend API: https://api.cryptobalancer.eu

---

## Helm-based Deployment Guide

### Prerequisites

- Kubernetes cluster (v1.25+ recommended)
- Helm installed ([Helm Quickstart Guide](https://helm.sh/fr/docs/intro/quickstart/))
- kubectl configured for your cluster
- Traefik Ingress Controller and cert-manager installed

### 1. Namespace Setup

```sh
kubectl create namespace cryptobalancer
```

### 2. Deploy cert-manager ClusterIssuer

```sh
helm upgrade --install cert-manager ./helm/cert-manager -n cryptobalancer
kubectl get clusterissuer letsencrypt-prod
```

### 3. Deploy Backend and Frontend

```sh
helm upgrade --install backend ./helm/backend -n cryptobalancer
helm upgrade --install frontend ./helm/frontend -n cryptobalancer
```

### 4. Check Deployments

```sh
kubectl -n cryptobalancer get deployments
kubectl -n cryptobalancer get pods
kubectl -n cryptobalancer get svc
kubectl -n cryptobalancer get ingress
```

### 5. Verify TLS Certificates

```sh
kubectl -n cryptobalancer get certificates
kubectl -n cryptobalancer describe certificate <certificate-name>
```

### 6. Test Application Access

- Frontend: https://cryptobalancer.eu
- Backend API: https://api.cryptobalancer.eu

### 7. Upgrade or Rollback

```sh
helm upgrade backend ./helm/backend -n cryptobalancer
helm rollback backend <REVISION> -n cryptobalancer
```

### 8. Uninstall

```sh
helm uninstall backend -n cryptobalancer
helm uninstall frontend -n cryptobalancer
helm uninstall cert-manager -n cryptobalancer
```

### 9. Troubleshooting

- Use `helm status <release> -n cryptobalancer` for release status.
- Use `kubectl describe` and `kubectl logs` for deeper debugging.
- Validate your charts with `helm lint ./helm/<chart>` before deploying.

---

## (Optional) Legacy Deployment Guide

> **Note:** The following steps are for reference only. Helm is now the recommended and supported deployment method.

1. Create the namespace:
   ```sh
   kubectl create namespace cryptobalancer
   ```
2. Apply the cert-manager configuration:
   ```sh
   kubectl apply -f base/cert-manager/cluster-issuer.yaml
   ```
3. Deploy the backend components:
   ```sh
   kubectl apply -f base/backend/
   ```
4. Deploy the frontend components:
   ```sh
   kubectl apply -f base/frontend/
   ```
5. Check status and verify as above.

---

## 📞 Contact

For questions about this infrastructure, please contact [xlillium@cryptobalancer.eu](mailto:xlillium@cryptobalancer.eu).
