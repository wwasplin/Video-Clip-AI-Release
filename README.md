# Video Clip AI – Deploy from tarball

This repo contains pre-built deploy tarballs. Use them to install Video Clip AI on a server without building from source.

## Get the tarball

- Open the **releases/** folder in this repo.
- Download **`video-clip-ai_<version>_obfuscated.tar.gz`** (e.g. via clone, or from the GitHub UI).

## Deploy on the server

1. **Copy the tarball to the server** (e.g. with `scp` or download directly on the server).

2. **Extract** to the install directory (e.g. `/opt/video-clip-ai`):

   ```bash
   sudo mkdir -p /opt/video-clip-ai
   sudo tar -xzf video-clip-ai_1.0.0_obfuscated.tar.gz -C /opt/video-clip-ai
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

- Linux (e.g. Ubuntu/Debian)
- Python 3.10+
- PostgreSQL
- FFmpeg
- Node.js and npm (only for running `npx serve` for the frontend; the frontend is pre-built, no build step)

No need to build the frontend or run PyArmor on the server; the tarball contains the built UI and the backend.
