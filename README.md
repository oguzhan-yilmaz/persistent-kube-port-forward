# persistent-kube-port-forward

A Docker Compose solution to maintain persistent, auto-reconnecting `kubectl port-forward` tunnels for local development.

## Prerequisites

* Docker and Docker Compose
* A valid `kubeconfig` file

## Setup

1. **Configure Environment**
Create a `.env` file from the example.

```bash
cp .env.example .env
```


2. **Set Variables**

Edit `.env` to match your local kubeconfig location and cluster defaults.

```bash
# Path to the directory containing your config (e.g., ~/.kube)
LOCAL_KUBE_DIR=~/.kube

# Specific filename to use (e.g., config, k3s.yaml)
KUBE_FILENAME=config

# Default Cluster Context and Namespace
DEFAULT_CTX=my-cluster-context
DEFAULT_NS=default
```



## Usage

**Start Tunnels**
Run in detached mode.

```bash
docker compose up -d

```

**Stop Tunnels**
Stops and removes containers.

```bash
docker compose down

```

**Check Status**
View the connection status of specific tunnels.

```bash
# All logs
docker compose logs -f

# Specific tunnel logs
docker compose logs -f dev-backend

```

## Adding New Tunnels

To add a new port forward, append a new service block to `docker-compose.yml`. Inherit from `x-tunnel-base` and define the specific service targets.

**Example Block:**

```yaml
  new-service-tunnel:
    <<: *tunnel-base
    container_name: dev-new-service
    ports:
      - "9090:9090"
    environment:
      - SVC=target-service-name
      - PORTS=9090:80
      # Optional: Override defaults
      # - NS=other-namespace

```

**Variables:**

* `SVC`: The Kubernetes Service name.
* `PORTS`: The port mapping formatted as `LOCAL_PORT:REMOTE_PORT`.
* `NS` (Optional): Override the default namespace defined in `.env`.