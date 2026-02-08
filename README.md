# Video Clip AI - Release

Pre-built .deb packages for Ubuntu/Debian.

## Install

```bash
sudo dpkg -i releases/video-clip-ai_1.0.0_all.deb
```

## Requirements

- Ubuntu 22.04+ or Debian 12+
- Python 3.10+, Node.js 18+, PostgreSQL 14+, FFmpeg
- NVIDIA GPU with CUDA (optional, CPU fallback available)

## After Install

1. Edit `/opt/video-clip-ai/.env` (POSTGRES_PASSWORD, SECRET_KEY)
2. Set up PostgreSQL:
   ```bash
   sudo -u postgres createuser clipai -P
   sudo -u postgres createdb video_clip_ai
   ```
3. Start services:
   ```bash
   sudo systemctl start video-clip-ai-backend video-clip-ai-frontend
   ```
4. Open http://localhost:3020 in your browser

## Source

Source code: https://github.com/wwasplin/video-clip-ai
