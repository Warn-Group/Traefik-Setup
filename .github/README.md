# Traefik-Setup

## Setup

As root.

### Clone

```bash
cd /etc && git clone https://github.com/Warn-Group/Traefik-Orchestrator traefik
```

### File structure

```
/etc/traefik
    ├── acme.json # (generated)
    ├── cloudflare.env
    ├── compose.yml
    ├── logs # (generated)
    │   ├── access.log # (generated)
    │   └── traefik.log # (generated)
    └── traefik.yml
```

> [!NOTE]  
> Files and folders noted as generated will be generated **once** traefik starts.

### Configure the environment

Inside `cloudflare.env`:

- Add your Cloudflare email to the `CF_API_EMAIL` field.
- Create and add a [Cloudflare API Key](https://dash.cloudflare.com/profile/api-tokens) to the `CF_API_KEY` field.

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

## Add a project

In your project (docker) `compose.yml` file add the following lines:

```yml
    networks:
      - traefik-bridge
    labels:
      - traefik.enable=true
      - traefik.http.routers.server-name.entrypoints=web, websecure
      - traefik.http.routers.server-name.rule=Host(`your.domain.com`)
      - traefik.http.services.server-name.loadbalancer.server.port=8000
      - traefik.http.routers.server-name.tls=true
      - traefik.http.routers.server-name.tls.certresolver=cloudflare
      # - traefik.http.routers.server-name.tls.domains[0].main=your.domain.com
      # - traefik.http.routers.server-name.tls.domains[0].sans=*.domain.com #(optional)

networks:
  traefik-bridge:
    external: true
```

> [!IMPORTANT]  
> In every project, replace `server-name` with a **unique** name/id that will represent your project/deployment.

## Examples

Few examples using this repository traefik setup.

- [Moon-API with Traefik](https://github.com/Warn-Group/Traefik-Moon-API)
