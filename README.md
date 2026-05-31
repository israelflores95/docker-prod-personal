# Home Server Stack (casa-flores.net)

This repository contains a Docker Compose template for a home server sitting behind a CGNAT, utilizing Cloudflare Tunnels for secure external access.

## Quick Start

### 1. Configure Environment
Copy the example environment file and update it with your settings:
```bash
cp .env.example .env
```
Edit `.env` and set:
- `CLOUDFLARE_TUNNEL_TOKEN`: Your unique tunnel token.
- `PUID`/`PGID`: Your local user IDs (run `id` in your terminal).

### 2. Set Up Cloudflare Tunnel
1. Go to the [Cloudflare Zero Trust Dashboard](https://one.dash.cloudflare.com/).
2. Navigate to **Networks > Tunnels**.
3. Click **Create a Tunnel**.
4. Give it a name (e.g., `casa-flores-home`).
5. Choose **Docker** as the connector.
6. Copy the **Token** provided in the docker run command and paste it into your `.env` file.

### 3. Launch the Stack
```bash
docker compose up -d
```

## Routing Services
To expose services to the internet, add **Public Hostnames** in your Cloudflare Tunnel settings:

| Public Hostname | Service Type | URL (Internal) |
| :--- | :--- | :--- |
| `dashboard.casa-flores.net` | `HTTP` | `homarr:7575` |
| `sonarr.casa-flores.net` | `HTTP` | `sonarr:8989` |

> **Note:** Because all containers are on the `cf-tunnel` network, you can route traffic using the **container name** and internal port.

## Services Included
- **cloudflared**: The tunnel agent.
- **Homarr**: A sleek dashboard for all your services.
- **Media Stack (Commented out)**: Plex, Sonarr, Radarr.
