# Traefik-Orchestrator

## Setup

As root.

### Clone

```bash
cd /etc && git clone https://github.com/Warn-Group/Traefik-Orchestrator traefik
```

### File structure

```
/etc/traefik
    ├── compose.yml
    ├── logs # (generated)
    │   ├── access.log # (generated)
    │   └── traefik.log # (generated)
    └── traefik.yml
```

> [!NOTE]  
> Files and folders noted as generated will be generated once traefik start.

### Create Docker Network

Traefik need an external docker network to properly redirect traffic, create one using the following command:

```
docker network create traefik-bridge
```

> [!IMPORTANT]  
> `traefik-bridge` is the network name already configured in every configuration of this repository.

### Start Traefik

Inside `/etc/traefik` run the following command:

```
docker compose up -d
```

> [!NOTE]  
> Once started, the traefik-orchestrator will restart automatically each time the docker daemon restart. 