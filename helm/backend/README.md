# Backend Helm Chart

This Helm chart deploys the backend API application for the CryptoBalancer platform.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Traefik Ingress Controller installed in the cluster
- cert-manager installed in the cluster (for TLS certificates)

## Installation

```bash
helm install backend ./helm/backend
```

## Configuration

The following table lists the configurable parameters of the backend chart and their default values.

| Parameter           | Description                                | Default                                    |
| ------------------- | ------------------------------------------ | ------------------------------------------ |
| `namespace`         | Namespace where resources will be deployed | `cryptobalancer`                           |
| `replicaCount`      | Number of replicas to deploy               | `2`                                        |
| `image.repository`  | Container image repository                 | `ghcr.io/xlillium/test-gh-deploy-backend`  |
| `image.pullPolicy`  | Image pull policy                          | `Always`                                   |
| `image.tag`         | Image tag                                  | `a94a8977771d8c31680f317e9a8d141264dd6aa3` |
| `service.type`      | Service type                               | `ClusterIP`                                |
| `service.port`      | Service port                               | `80`                                       |
| `ingress.enabled`   | Enable ingress                             | `true`                                     |
| `ingress.className` | Ingress class name                         | `traefik`                                  |
| `ingress.hosts`     | List of ingress hosts                      | See values.yaml                            |
| `ingress.tls`       | Ingress TLS configuration                  | See values.yaml                            |
| `resources`         | CPU/Memory resource requests/limits        | See values.yaml                            |
| `securityContext`   | Security context for pods                  | See values.yaml                            |
| `livenessProbe`     | Liveness probe configuration               | `{}`                                       |
| `readinessProbe`    | Readiness probe configuration              | `{}`                                       |

## Security Considerations

The deployment follows security best practices:

- Running as non-root user (UID 1000)
- Dropping all capabilities
- Preventing privilege escalation
- Setting security headers in Ingress
- Using TLS
