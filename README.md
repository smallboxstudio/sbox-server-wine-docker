# sbox-server-wine-docker
s&box dedicated server container using Alpine Linux & Wine

## Quick Setup
```
# Latest Public Build
docker run giodotblue/sbox-server:latest +game facepunch.walker garry.scenemap +hostname "Mon Serveur"

# Latest Staging Build
docker run giodotblue/sbox-server:staging-latest +game facepunch.walker garry.scenemap
```

## Build from Source

Requires [Docker](https://docs.docker.com/engine/install/) installed.

### 1. Configure your Steam credentials

The build needs a Steam account that owns s&box to download the dedicated server.

Open `images/sbox-server/Dockerfile` and fill in your credentials:
```dockerfile
ARG STEAM_USER="your_steam_username"
ARG STEAM_PASS="your_steam_password"
```

> **Warning:** Ne commitez jamais vos credentials dans un repo public. Utilisez des build args à la place:
> ```bash
> docker build -f images/sbox-server/Dockerfile \
>   --build-arg STEAM_USER="your_username" \
>   --build-arg STEAM_PASS="your_password" \
>   -t sbox-server .
> ```

### 2. Build the image

```bash
docker build -f images/sbox-server/Dockerfile -t sbox-server .
```

During the build, Steam Guard will ask you to confirm the login via the Steam mobile app.

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
# sbox-server-wine-docker
# sbox-server-wine-docker
