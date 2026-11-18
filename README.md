# Mattermost ARM64 Docker Images

Automated ARM64 (`aarch64`) Docker builds for [Mattermost](https://mattermost.com/), since the official images only support `amd64`.

## Why this exists

I wanted to run Mattermost on my K3s cluster hosted on Oracle Cloud's free tier ARM instances (Ampere A1). Turns out Mattermost doesn't publish ARM64 Docker images — only `amd64`. The binaries are there on their release server, they just never package them into a container image. So I built this to do it automatically.

If you're running ARM hardware (Oracle Cloud, Raspberry Pi, AWS Graviton, Hetzner Arm64, etc.) and want Mattermost, this repo has you covered.

Uses official Mattermost ARM64 binaries from [releases.mattermost.com](https://releases.mattermost.com/) — no source compilation needed.

## Usage

```bash
docker pull ghcr.io/antonsatt/mattermost-arm64:latest
```

Or a specific version:

```bash
docker pull ghcr.io/antonsatt/mattermost-arm64:11.5.0
```

## How it works

A GitHub Actions workflow runs weekly (every Monday) and:

1. Finds the highest stable Mattermost release by version number (not GitHub's "latest" flag, which often points at a patch on an older line)
2. Also finds the newest patch within the major version my own cluster runs, so a deployment that stays on an older major still gets its security patches
3. Skips any version whose image already exists in GHCR
4. Verifies the official ARM64 tarball is available
5. Builds and pushes to GitHub Container Registry, and points `:latest` at the highest release
6. Bumps my cluster's manifest to the newest patch within its major (major upgrades stay manual)
7. Re-enables itself through the API, because GitHub disables scheduled workflows after 60 days without commits

You can also trigger a build manually from the Actions tab, with an optional `mm_version`.

## Building locally

```bash
docker build --build-arg MM_VERSION=11.5.0 -t mattermost-arm64:11.5.0 .
```

## Kubernetes

```yaml
image: ghcr.io/antonsatt/mattermost-arm64:11.5.0
```


Anton :) 
