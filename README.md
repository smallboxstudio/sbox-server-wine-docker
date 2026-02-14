# sbox-server-wine-docker
s&box dedicated server container using Alpine Linux & Wine

## Quick Setup
```
# Build local image (required before running locally)
docker build -f images/sbox-server/Dockerfile -t sbox-server .

# Run local image (after build)
docker run -it sbox-server +game facepunch.walker garry.scenemap +hostname "Mon Serveur"
```

## Build from Source

Requires [Docker](https://docs.docker.com/engine/install/) installed.

### 1. Configure your Steam credentials

The build needs a Steam account that owns s&box to download the dedicated server.

**Ne mettez pas vos identifiants en dur dans le Dockerfile.** Gardez les `ARG` tels quels et passez vos credentials au build via `--build-arg` (ou via un fichier `.env` avec Docker Compose).

```dockerfile
ARG STEAM_USER=""
ARG STEAM_PASS=""
```

Exemple avec `docker build`:
```bash
docker build -f images/sbox-server/Dockerfile \
	--build-arg STEAM_USER="your_username" \
	--build-arg STEAM_PASS="your_password" \
	-t sbox-server .
```

Exemple avec `docker compose` (fichier `.env` à la racine):
```bash
STEAM_USER=your_username
STEAM_PASS=your_password
```

```yaml
services:
	server:
		build:
			context: .
			dockerfile: images/sbox-server/Dockerfile
			args:
				STEAM_USER: ${STEAM_USER}
				STEAM_PASS: ${STEAM_PASS}
```

### 2. Build the image

Commande de build (recommandée) :
```bash
docker build -f images/sbox-server/Dockerfile -t sbox-server .
```

During the build, Steam Guard will ask you to confirm the login via the Steam mobile app.

### 3. Deploy to a remote server

If you built the image locally and want to deploy it on another machine:
```bash
# On your local machine
docker save sbox-server | gzip > sbox-server.tar.gz
scp sbox-server.tar.gz user@your-server:/root/

# On the remote server
docker load < sbox-server.tar.gz
```

## Run

```bash
docker run -it sbox-server +game facepunch.walker garry.scenemap +hostname "Mon Serveur"
```

### Server Arguments

All arguments are passed directly to `sbox-server.exe`:

| Argument | Description |
|---|---|
| `+game <gamemode>` | Game mode to load (e.g. `facepunch.walker`) |
| `+hostname <name>` | Server name |

## Architecture

The image is built in two stages:
1. **Builder** (Debian) - Installs steamcmd, Wine, winetricks (win10, vcrun2022, dotnet10), and downloads the s&box dedicated server (app 1892930)
2. **Runtime** (Alpine) - Lightweight image with only Wine and gnutls, runs the server via Wine
