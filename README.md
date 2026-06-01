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

### 2. Set Up Cloudflare Tunneld
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
| `private.casa-flores.net` | `HTTP` | `internal-app:8000` |

> **Note:** Because all containers are on the `cf-tunnel` network, you can route traffic using the **container name** and internal port.

## Securing Internal Services (Cloudflare Access)
For services like `internal-app` that you want to keep private but access with a valid SSL:

1.  In the Cloudflare Dashboard, go to **Zero Trust > Access > Applications**.
2.  Click **Add an Application** and choose **Self-hosted**.
3.  Enter the subdomain (e.g., `private.casa-flores.net`).
4.  Configure a **Policy** (e.g., "Allow my email address").
5.  Now, whenever you visit that URL, Cloudflare will ask you to log in before granting access to your home server.

## Services Included
- **cloudflared**: The tunnel agent.
- **Homarr**: A sleek dashboard for all your services.
- **Media Stack (Commented out)**: Plex, Sonarr, Radarr.

## 🎓 Docker Basics (For Beginners)

If you're new to Docker, here are the three most important things to know about this setup:

1.  **Containers are "Stateless"**: This means if you delete a container and recreate it, all your files inside it are gone **unless** you use a **Volume**. In our `compose.yml`, we use `volumes` to map a folder on your computer to a folder inside the container so your settings are saved.
2.  **Images are Blueprints**: The `image: ...` line tells Docker which pre-made software to download. You can find thousands of these on [Docker Hub](https://hub.docker.com/).
3.  **Networks are Rooms**: By putting all our apps on the `cf-tunnel` network, we've put them in a private virtual room. The `cloudflared` container can see the `homarr` container just by calling its name, like a person in the same room.
