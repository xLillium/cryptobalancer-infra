# CryptoBalancer Platform Helm Chart

This umbrella Helm chart deploys the complete CryptoBalancer platform including:

- Frontend UI
- Backend API
- Certificate Management

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Traefik Ingress Controller installed in the cluster
- DNS records configured for your domains

## Installation

### Add the Helm Repository

```bash
# Clone the repository
git clone https://github.com/your-org/cryptobalancer-infra.git
cd cryptobalancer-infra
```

### Install the Chart

```bash
# Create the namespace
kubectl create namespace cryptobalancer

# Install the umbrella chart
helm install cryptobalancer ./helm/cryptobalancer
```

## Configuration

The umbrella chart includes all configurations for the individual components. For detailed configuration options, see the individual chart READMEs:

- [Frontend](../frontend/README.md)
- [Backend](../backend/README.md)

### Global Parameters

| Parameter          | Description                     | Default             |
| ------------------ | ------------------------------- | ------------------- |
| `global.namespace` | Namespace for all resources     | `cryptobalancer`    |
| `global.domain`    | Base domain for the application | `cryptobalancer.eu` |

## Architecture

The platform uses a microservices architecture with:

- **Frontend**: User interface accessible at cryptobalancer.eu
- **Backend**: API accessible at api.cryptobalancer.eu
- **cert-manager**: Manages TLS certificates for secure communications

## Security

The deployment implements several security best practices:

- Non-root container execution
- Resource limits to prevent resource exhaustion attacks
- Network isolation with ingress controllers
- TLS encryption for all external traffic
- Security headers in HTTP responses

## Upgrading

To upgrade the platform:

```bash
helm upgrade cryptobalancer ./helm/cryptobalancer
```

## Uninstallation

To remove the CryptoBalancer platform:

```bash
helm uninstall cryptobalancer
```

This will remove all resources except persistent volumes. To remove persistent data, you'll need to delete the PVCs manually.
