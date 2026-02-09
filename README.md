# Video Clip AI - Release

Pre-built .deb packages for Ubuntu/Debian.

**Clone this repo (HTTPS preferred on fresh instances; SSH requires host key setup):**
```bash
git clone https://github.com/wwasplin/Video-Clip-AI-Release.git
cd Video-Clip-AI-Release
```

## Before install: update system and install requirements

1. Update the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

2. Install dependencies.
   - **Ubuntu 24.04** (use fuse3 and libfuse2t64; fuse + libfuse2 conflicts with sshfs):
     ```bash
     sudo apt install -y python3 python3-venv python3-pip nodejs npm \
       postgresql postgresql-contrib ffmpeg \
       cifs-utils sshfs fuse3 libfuse2t64 pkg-config cmake build-essential
     ```
   - **Older Ubuntu/Debian** (e.g. 22.04, Debian 12):
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

**Note:** Post-install runs `pip install -r requirements.txt` in the backend venv (PyTorch and others). This can take **10–15+ minutes**. Do not interrupt. If it is interrupted or fails, recover with:
```bash
sudo /opt/video-clip-ai/backend/venv/bin/pip install -r /opt/video-clip-ai/backend/requirements.txt
sudo dpkg --configure video-clip-ai
```

## After install

1. **Edit `/opt/video-clip-ai/.env`**  
   Set `POSTGRES_PASSWORD`, `SECRET_KEY`, and any other options.  
   - Default PostgreSQL password in the packaged example is **clipper**.  
   - Generate a secure `SECRET_KEY`:
     ```bash
     openssl rand -hex 32
     ```
     Then in `.env` set e.g. `SECRET_KEY=<paste-output>`. If using `sed`, use a delimiter that is not `/` (e.g. `sed 's#^SECRET_KEY=.*#SECRET_KEY=your_key_here#'`).

2. **Set up PostgreSQL** (use the same password as in `.env`):
   ```bash
   sudo -u postgres createuser clipai -P
   sudo -u postgres createdb video_clip_ai
   sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE video_clip_ai TO clipai;"
   sudo -u postgres psql -c "ALTER DATABASE video_clip_ai OWNER TO clipai;"
   ```

3. **Start services:**
   ```bash
   sudo systemctl start video-clip-ai-backend video-clip-ai-frontend
   ```

4. Open **http://localhost:3020** in your browser.

The package ships only **frontend/dist/** (built static files). The frontend systemd unit serves them with `npx serve`; there is no `package.json` or dev server in the package.

## Troubleshooting

- **Backend SEGV (crash with PyArmor/native):** The backend is distributed with PyArmor-obfuscated Python. If you see a segmentation fault, it may be an environment/ABI mismatch. Use a supported environment (e.g. same Ubuntu version as the build) or request a non-obfuscated build if needed.
- **Frontend fails / missing package.json:** The release contains only the built app in `frontend/dist/`. The unit runs `npx serve -s . -l 3020` in that directory. Do not run `npm run dev`; there is no dev setup in the package.
- **dpkg dependency errors:** Install the dependencies listed in “Before install” (python3, nodejs, postgresql, ffmpeg, fuse3/libfuse2t64 or fuse/libfuse2, etc.) then run `sudo dpkg -i ...` again.
- **Venv / pip install failed or interrupted:** Run the recovery commands from the “Install” section above.
