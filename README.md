# CryptoBalancer Infrastructure

[![Kubernetes](https://img.shields.io/badge/kubernetes-v1.25+-green)](https://kubernetes.io/)
[![Traefik](https://img.shields.io/badge/ingress-traefik-orange)](https://traefik.io/)
[![cert-manager](https://img.shields.io/badge/certificates-cert--manager-blueviolet)](https://cert-manager.io/)

Kubernetes infrastructure configuration for the CryptoBalancer application.

## 📋 Repository Contents

This repository contains the Kubernetes manifests required to deploy the CryptoBalancer application infrastructure:

```
base/
├── backend/
│   ├── deployment.yaml    # Backend pod deployment configuration
│   ├── ingress.yaml       # Ingress rules for api.cryptobalancer.eu
│   └── service.yaml       # Service exposing the backend pods
├── frontend/
│   ├── deployment.yaml    # Frontend pod deployment configuration
│   ├── ingress.yaml       # Ingress rules for cryptobalancer.eu
│   └── service.yaml       # Service exposing the frontend pods
└── cert-manager/
    └── cluster-issuer.yaml  # Let's Encrypt certificate issuer configuration
```

## 🏗️ Infrastructure Design

### Namespace and Isolation

All components are deployed in the `cryptobalancer` namespace for logical separation and access control.

### Components

#### Backend Service
- **Deployment**: 2 replicas for high availability
- **Container**: Backend API running on port 8080
- **Resources**:
    - Requests: 100m CPU, 256Mi memory
    - Limits: 500m CPU, 512Mi memory
- **Security**: Non-root user, capability dropping, and privilege escalation prevention

#### Frontend Service
- **Deployment**: 2 replicas for high availability
- **Container**: Web interface running on NGINX (port 80)
- **Resources**:
    - Requests: 50m CPU, 128Mi memory
    - Limits: 200m CPU, 256Mi memory
- **Security**: Non-root user (nginx user 101), capability dropping, and privilege escalation prevention

#### Networking
- **Services**: ClusterIP type for internal routing
- **Ingress**: Traefik-based routing with automatic HTTPS
    - Frontend: `cryptobalancer.eu`
    - Backend: `api.cryptobalancer.eu`

#### TLS Configuration
- **cert-manager**: Automatic certificate provisioning and renewal
- **Let's Encrypt**: Production ACME server for trusted certificates
- **Challenge Method**: HTTP-01 with Traefik solver

### Update Strategy

Both services use RollingUpdate strategy with:
- `maxSurge: 1` - Allow one extra pod during updates
- `maxUnavailable: 0` - Ensure no downtime during updates

## 🔒 Security Measures

The infrastructure implements several security best practices:

1. **TLS Everywhere**: Automatic HTTPS with Let's Encrypt certificates
2. **Container Security Context**:
    - Non-root execution
    - Capability dropping (ALL)
    - Privilege escalation prevention
3. **Security Headers**:
    - X-Frame-Options: DENY
    - X-Content-Type-Options: nosniff
    - X-XSS-Protection: 1; mode=block
    - Referrer-Policy: strict-origin-when-cross-origin
    - Content-Security-Policy: default-src 'self'
4. **HTTPS Enforcement**:
    - SSL redirect
    - Force SSL redirect
5. **Request Limiting**: 10MB maximum request size

## 🚀 Deployment Guide

### Prerequisites

- Kubernetes cluster v1.25+
- Traefik Ingress Controller installed
- cert-manager v1.8+ installed
- kubectl configured with access to your cluster

### Deployment Steps

1. Create the namespace:
   ```bash
   kubectl create namespace cryptobalancer
   ```

2. Apply the cert-manager configuration:
   ```bash
   kubectl apply -f base/cert-manager/cluster-issuer.yaml
   ```

3. Deploy the backend components:
   ```bash
   kubectl apply -f base/backend/
   ```

4. Deploy the frontend components:
   ```bash
   kubectl apply -f base/frontend/
   ```

### Verification

Check the deployment status:
```bash
kubectl -n cryptobalancer get pods,svc,ingress
```

Verify certificate issuance:
```bash
kubectl -n cryptobalancer get certificates
```

## 📝 Configuration Details

### Container Images

- Backend: `ghcr.io/xlillium/test-gh-deploy-backend:a94a8977771d8c31680f317e9a8d141264dd6aa3`
- Frontend: `ghcr.io/xlillium/test-gh-deploy-frontend:1589cd8aef1a5d320b3192d0689f8a53154bde12`

### Exposed Services

- **Frontend**: `https://cryptobalancer.eu`
- **Backend API**: `https://api.cryptobalancer.eu`

## 📞 Contact

For questions about this infrastructure, please contact [xlillium@cryptobalancer.eu](mailto:xlillium@cryptobalancer.eu).
