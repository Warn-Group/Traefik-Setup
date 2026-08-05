# Traefik-Setup

## Setup

As root.

### Clone

```bash
git clone https://github.com/Warn-Group/Traefik-Setup traefik
```

### File structure

```
traefik
    ├── acme.json # (generated)
    ├── cloudflare.env
    ├── compose.yaml
    ├── logs # (generated)
    │   ├── access.log # (generated)
    │   └── traefik.log # (generated)
    └── traefik.yaml
```

> [!NOTE]  
> Files and folders noted as generated will be generated **once** traefik starts.

### Configure the environment

Inside `cloudflare.env`:

#### Recommended:

- Create a [Cloudflare API Token](https://dash.cloudflare.com/profile/api-tokens) with:
  - **Permissions**: Zone: DNS: Edit
  - **Zone Resource**: Select the domain(s) you'll manage
- Add the generated Token to the `CLOUDFLARE_DNS_API_TOKEN` field.

#### Alternative:

- Add your Cloudflare email to the `CLOUDFLARE_EMAIL` field.
- Add your [Cloudflare Global API Key](https://dash.cloudflare.com/profile/api-tokens) to the `CLOUDFLARE_API_KEY` field.

> [!IMPORTANT]
> Use **only one method**. Comment out the other to avoid conflicts.

### Setup Docker

If you haven't already, initialize Docker Swarm using the following command:

```
docker swarm init
```

Traefik need an external docker network to properly redirect traffic, create one using the following command:

```
docker network create --driver overlay --attachable traefik-bridge
```

> [!IMPORTANT]  
> `traefik-bridge` is the network name already configured in every configuration of this repository.

### Start Traefik

Inside `/etc/traefik` run the following command:

```
docker stack deploy -c compose.yaml traefik
```

> [!NOTE]  
> Once started, the traefik-orchestrator will restart automatically each time the docker daemon restart. 

## Add a project

In your project (docker) `compose.yaml` file add the following lines:

```yml
    networks:
      - traefik-bridge
    deploy:
      labels:
        - traefik.enable=true
        - traefik.http.services.service-name.loadbalancer.server.port=3000
        - traefik.http.routers.service-name.entrypoints=web,websecure
        - traefik.http.routers.service-name.middlewares=httpsredirect@file
        - traefik.http.routers.service-name.rule=Host(`example.com`)
        - traefik.http.routers.service-name.tls=true
        - traefik.http.routers.service-name.tls.certresolver=cloudflare
        - traefik.http.routers.service-name.tls.domains[0].main=example.com
        - traefik.http.routers.service-name.tls.domains[0].sans=www.example.com

networks:
  traefik-bridge:
    external: true
```

Most labels used here are examples and not mandatory.

> [!IMPORTANT]  
> In every project, replace `service-name` with a **unique** name/id that will represent your project/deployment.

## Examples

Few examples using this repository traefik setup.

- [Moon-API with Traefik](https://github.com/Warn-Group/Traefik-Moon-API)
