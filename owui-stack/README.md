# OpenWebUI Stack Helm Chart

This Helm chart deploys the complete OpenWebUI stack with all components, including LLM services, monitoring, and Model Context Protocol (MCP) tools.

## Overview

The OpenWebUI Stack includes the following components:

- **OpenWebUI**: Web interface for interacting with LLMs
- **Ollama**: Local LLM service
- **LiteLLM**: API proxy for multiple LLM providers
- **MCPO**: Model Context Protocol Orchestrator for tools
- **Prometheus & Grafana**: Monitoring stack
- **Loki & Promtail**: Log aggregation
- **Mimir**: Metrics storage
- **Node Exporter & cAdvisor**: System metrics collection
- **Alertmanager**: Alerting system

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure
- For GPU support: NVIDIA GPUs with drivers installed

## Getting Started

### Add the Repository

```bash
# Assuming you have the chart locally
cd D:\git\owui_open_2
```

### Install the Chart

```bash
helm install owui-stack ./helm-charts/owui-stack -n openwebui --create-namespace
```

### Override Default Values

Create a custom values file:

```bash
helm install owui-stack ./helm-charts/owui-stack -n openwebui --create-namespace -f my-values.yaml
```

## Accessing the UI

After the installation completes, follow the instructions in the NOTES that are displayed to access the OpenWebUI interface.

### Accessing via Port Forward

```bash
kubectl port-forward -n openwebui svc/openwebui 8080:8080
```

Then access the UI at http://localhost:8080

## Configuration

The following table lists the configurable parameters of the chart and their default values. See the `values.yaml` file for more detailed descriptions.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `global.storageClass` | Storage class for all PVCs | `""` |
| `global.imagePullPolicy` | Image pull policy for all services | `IfNotPresent` |
| `global.namespace` | Kubernetes namespace | `openwebui` |
| `secrets.*` | API keys and credentials | See `values.yaml` |
| `ollama.enabled` | Enable Ollama service | `true` |
| `ollama.image.*` | Ollama image settings | See `values.yaml` |
| `ollama.service.*` | Ollama service settings | See `values.yaml` |
| `ollama.resources.*` | Ollama resources | See `values.yaml` |
| `ollama.persistence.*` | Ollama persistence settings | See `values.yaml` |
| `ollama.gpu.*` | Ollama GPU settings | See `values.yaml` |
| `litellm.enabled` | Enable LiteLLM service | `true` |
| `litellm.*` | LiteLLM settings | See `values.yaml` |
| `openwebui.enabled` | Enable OpenWebUI | `true` |
| `openwebui.*` | OpenWebUI settings | See `values.yaml` |
| `mcpo.enabled` | Enable MCP Orchestrator | `true` |
| `mcpo.*` | MCPO settings | See `values.yaml` |
| `prometheus.enabled` | Enable Prometheus | `true` |
| `prometheus.*` | Prometheus settings | See `values.yaml` |
| `nodeExporter.enabled` | Enable Node Exporter | `true` |
| `nodeExporter.*` | Node Exporter settings | See `values.yaml` |
| `cadvisor.enabled` | Enable cAdvisor | `true` |
| `cadvisor.*` | cAdvisor settings | See `values.yaml` |
| `grafana.enabled` | Enable Grafana | `true` |
| `grafana.*` | Grafana settings | See `values.yaml` |
| `alertmanager.enabled` | Enable Alertmanager | `true` |
| `alertmanager.*` | Alertmanager settings | See `values.yaml` |
| `loki.enabled` | Enable Loki | `true` |
| `loki.*` | Loki settings | See `values.yaml` |
| `promtail.enabled` | Enable Promtail | `true` |
| `promtail.*` | Promtail settings | See `values.yaml` |
| `mimir.enabled` | Enable Mimir | `true` |
| `mimir.*` | Mimir settings | See `values.yaml` |

## Building Custom MCPO Image

By default, the chart assumes you have a local image for MCPO. To build it:

```bash
cd D:\git\owui_open_2\mcp\mcpo
docker build -t mcpo:latest .
```

Make sure your Kubernetes cluster can access this image.

## GPU Support

To use GPUs with Ollama, ensure:
1. GPU drivers are installed on your nodes
2. The [NVIDIA Device Plugin](https://github.com/NVIDIA/k8s-device-plugin) is installed

Then set `ollama.gpu.enabled` to `true` in your values file.

## Persistence

This chart uses PersistentVolumeClaims to store data. You can specify the storage class and size for each component in the values file.

## Uninstalling the Chart

To uninstall/delete the deployment:

```bash
helm uninstall owui-stack -n openwebui
```

This will delete all the Kubernetes resources associated with the chart, but PVCs will remain to prevent data loss. To delete them:

```bash
kubectl delete pvc --namespace openwebui --all
```

## Troubleshooting

### Common Issues

1. **Pod Startup Failures**: Check pod logs and events
   ```bash
   kubectl describe pod -n openwebui <pod-name>
   kubectl logs -n openwebui <pod-name>
   ```

2. **GPU Not Working**: Verify NVIDIA plugin is properly installed
   ```bash
   kubectl get pods -n kube-system | grep nvidia
   ```

3. **PVC Issues**: Check if PVCs are bound
   ```bash
   kubectl get pvc -n openwebui
   ```

## Security

- Change default passwords after deployment
- Consider implementing network policies
- For production, use a proper secret management solution
