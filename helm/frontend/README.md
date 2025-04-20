# Frontend Helm Chart

This Helm chart deploys the frontend application for the CryptoBalancer platform.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Traefik Ingress Controller installed in the cluster
- cert-manager installed in the cluster (for TLS certificates)

## Installation

```bash
helm install frontend ./helm/frontend
```

## Configuration

The following table lists the configurable parameters of the frontend chart and their default values.

| Parameter           | Description                                | Default                                    |
| ------------------- | ------------------------------------------ | ------------------------------------------ |
| `namespace`         | Namespace where resources will be deployed | `cryptobalancer`                           |
| `replicaCount`      | Number of replicas to deploy               | `2`                                        |
| `image.repository`  | Container image repository                 | `ghcr.io/xlillium/test-gh-deploy-frontend` |
| `image.pullPolicy`  | Image pull policy                          | `Always`                                   |
| `image.tag`         | Image tag                                  | `1589cd8aef1a5d320b3192d0689f8a53154bde12` |
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

- Running as non-root user (UID 101)
- Dropping all capabilities
- Preventing privilege escalation
- Setting security headers in Ingress
- Using TLS
