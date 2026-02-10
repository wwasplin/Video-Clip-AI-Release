# Deploy build (no .deb)

You can ship an obfuscated or plain backend without building a Debian package.

## Build a deploy tree

From the project root:

```bash
# Obfuscated backend (default) – requires PyArmor: pip install pyarmor
./packaging/build-deploy.sh

# Plain backend (e.g. to avoid PyArmor SEGV on some servers)
./packaging/build-deploy.sh --no-obfuscate
```

Output is **`build/deploy/`** with:

- **backend/** – Python app (obfuscated or plain) + `requirements.txt`
- **frontend/dist/** – built static UI
- **.env.example**, **scripts/**, **systemd/** (production units), **install.sh**

## Deploy to a server

1. Copy the tree to the server (e.g. `/opt/video-clip-ai`):

   ```bash
   rsync -avz build/deploy/ user@server:/opt/video-clip-ai/
   ```

2. On the server, run the install script (creates venv, .env, optional systemd):

   ```bash
   ssh user@server 'cd /opt/video-clip-ai && ./install.sh'
   ```

3. Edit `/opt/video-clip-ai/.env` for PostgreSQL and other settings, then start services if you enabled systemd.

No Node/npm is required on the server; the frontend is pre-built and served with `npx serve` from `frontend/dist`.

## Push obfuscated build to release GitHub repo

From the project root, build the obfuscated deploy tree and push a tarball to the release repo:

```bash
./packaging/push-release.sh [version]
# default version 1.0.0; override with RELEASE_REPO=... if using an SSH host alias
```

This runs `build-deploy.sh`, creates `build/video-clip-ai_<version>_obfuscated.tar.gz`, then clones/updates the release repo, copies the tarball into `releases/`, and pushes. Users can clone the release repo and download the tarball, then extract and run `./install.sh` on the server.
