# Video Clip AI – Deploy from tarball

This repo contains pre-built deploy tarballs. Use them to install Video Clip AI on a server without building from source.

## Get the tarball

- Open the **releases/** folder in this repo.
- Download **`video-clip-ai_<version>_obfuscated.tar.gz`** (use the version that exists in releases/, e.g. `video-clip-ai_2.0.1_obfuscated.tar.gz`).

## System dependencies (install before running install.sh)

On Ubuntu/Debian, install these so the backend venv and dependencies (e.g. sentencepiece) build correctly:

```bash
sudo apt-get update
sudo apt-get install -y postgresql postgresql-contrib ffmpeg nodejs npm \
  python3-venv python3-dev pkg-config cmake build-essential
```

For the **obfuscated tarball**, Python 3.10 is recommended. On Ubuntu 24.04 (default Python 3.12), install 3.10 so the install script can use it for the backend venv:

```bash
sudo apt-get install -y python3.10 python3.10-venv python3.10-dev
```

## Deploy on the server

1. **Copy the tarball to the server** (e.g. with `scp` or download directly on the server).

2. **Extract** to the install directory (e.g. `/opt/video-clip-ai`):

   ```bash
   sudo mkdir -p /opt/video-clip-ai
   sudo tar -xzf video-clip-ai_2.0.1_obfuscated.tar.gz -C /opt/video-clip-ai
   ```

3. **Run the install script** (creates Python venv, installs dependencies, creates `.env`, optional systemd):

   ```bash
   cd /opt/video-clip-ai
   sudo ./install.sh
   ```

   When prompted, choose whether to install systemd services for autostart. The script will create `.env` from `.env.example`; you must configure it before the app can use the database.

4. **Edit `.env`** with your settings:

   - **POSTGRES_PASSWORD** – password for the PostgreSQL user (e.g. `clipai`).
   - **POSTGRES_HOST** – usually `localhost` if PostgreSQL is on the same machine.
   - Create the database and user if needed, for example:

     ```bash
     sudo -u postgres psql -c "CREATE USER clipai WITH PASSWORD 'your-password';"
     sudo -u postgres psql -c "CREATE DATABASE video_clip_ai OWNER clipai;"
     sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE video_clip_ai TO clipai;"
     ```

5. **Start the services** (if you installed systemd):

   ```bash
   sudo systemctl start video-clip-ai-backend video-clip-ai-frontend
   ```

6. **Open the app** in a browser at **http://\<server-ip\>:3020** and complete first-run setup (license, admin account).

## Requirements on the server

- **Ubuntu 22.04 or 24.04 LTS (x86_64)** – The obfuscated tarball is built in Ubuntu 22.04 for compatibility with current LTS releases.
- **Python 3.10+** with venv and dev headers (see **System dependencies** above). The PyArmor runtime in the tarball targets Python 3.10; use `python3.10` (or your default 3.10+) when the install script creates the backend venv.
- PostgreSQL, FFmpeg, Node.js and npm (for `npx serve`; frontend is pre-built).

No need to build the frontend or run PyArmor on the server; the tarball contains the built UI and the obfuscated backend.

## Troubleshooting

- **Backend crashes with SIGSEGV (segmentation fault)**  
  The obfuscated tarball is built for Ubuntu 22.04/24.04 LTS (x86_64) with Python 3.10. If the backend still segfaults (e.g. different distro or architecture), use a **non-obfuscated** tarball if published in **releases/**, or build from source: clone [Video-Clip-AI](https://github.com/wwasplin/video-clip-ai), run `./packaging/build-deploy.sh --no-obfuscate`, then copy `build/deploy/` to the server and run `./install.sh`.

- **"No license text available"**  
  The setup page shows this when the backend is not reachable (e.g. backend crashed or not started). Fix the backend (see above) and ensure it is running on port 3030.
