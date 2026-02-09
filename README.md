# Video Clip AI - Release

Pre-built .deb packages for Ubuntu/Debian.

## Before install: update system and install requirements

1. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install dependencies (Python, Node, PostgreSQL, FFmpeg, etc.):
   ```bash
   sudo apt install -y python3 python3-venv python3-pip nodejs npm \
     postgresql postgresql-contrib ffmpeg \
     cifs-utils sshfs fuse libfuse2 pkg-config cmake build-essential
   ```

3. Ensure PostgreSQL is running:
   ```bash
   sudo systemctl start postgresql
   sudo systemctl enable postgresql
   ```

## Install

```bash
sudo dpkg -i releases/video-clip-ai_1.0.0_all.deb
```

## After install

1. Edit `/opt/video-clip-ai/.env`: set `POSTGRES_PASSWORD`, `SECRET_KEY`, and any other options.  
   **Note:** The default PostgreSQL password in the packaged `.env.example` is **clipper**.

2. Set up PostgreSQL (use the same password as in `.env`):
   ```bash
   sudo -u postgres createuser clipai -P
   sudo -u postgres createdb video_clip_ai
   ```

3. Start services:
   ```bash
   sudo systemctl start video-clip-ai-backend video-clip-ai-frontend
   ```

4. Open http://localhost:3020 in your browser.
