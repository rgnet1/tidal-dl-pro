# 🌐 Tidal DL Pro — Web interface

A browser UI for **Tidal DL Pro**: FastAPI backend, Alpine.js + Tailwind frontend, and Docker packaging (this repository).

![Web Interface Preview](https://via.placeholder.com/800x400/3b82f6/ffffff?text=Tidal+DL+Pro+Web+UI)

## ✨ Features

### 🎨 Modern UI/UX
- **Clean, responsive design** built with React and Tailwind CSS
- **Dark/Light mode** support with system preference detection
- **Real-time updates** via WebSocket connections
- **Mobile-friendly** responsive design

### 🔐 Authentication
- **Secure TIDAL OAuth** integration
- **Token persistence** for seamless experience
- **Browser-based login** flow with automatic detection

### 🔍 Search & Browse
- **Advanced search** across tracks, albums, artists, playlists, and videos
- **Your TIDAL library** - access playlists, mixes, and favorites
- **Search results filtering** and sorting
- **Direct URL support** for TIDAL links

### 📥 Download Management
- **Download queue** with real-time status updates
- **Quality selection** for audio and video
- **Batch downloads** with progress tracking
- **Resume/pause** functionality

### ⚙️ Settings & Configuration
- **Comprehensive settings** panel
- **Quality preferences** (up to Hi-Res Lossless)
- **Download behavior** customization
- **Metadata options** (lyrics, cover art, etc.)

## 🚀 Quick Start with Docker

### Prerequisites
- Docker and Docker Compose installed
- A paid TIDAL subscription

### 1. Clone and Run
```bash
git clone https://github.com/rgnet1/tidal-dl-pro.git
cd tidal-dl-pro

mkdir -p config downloads
docker compose up -d --build

docker compose logs -f tidal-dl-pro-web
```

### 2. Access the Web Interface
Open your browser and navigate to:
```
http://localhost:8000
```

### 3. Login to TIDAL
1. Click "Login to TIDAL" 
2. Follow the OAuth flow in the popup
3. Return to the web interface after authentication

## 🛠️ Manual Installation

### Backend Setup
```bash
# Install Python dependencies
cd web/backend
pip install -r requirements.txt

# Start the FastAPI server
python main.py
```

### Frontend Setup
```bash
# Install Node.js dependencies
cd web/frontend
npm install

# Start development server
npm start
```

The backend will run on `http://localhost:8000` and the frontend on `http://localhost:3000` in development mode.

## 📁 Project Structure

```
tidal-dl-ng/
├── web/
│   ├── backend/
│   │   ├── main.py              # FastAPI application
│   │   └── requirements.txt     # Python dependencies
│   └── frontend/
│       ├── src/
│       │   ├── App.js          # Main React component
│       │   ├── components/     # React components
│       │   │   ├── AuthModal.js
│       │   │   ├── SearchResults.js
│       │   │   ├── UserLibrary.js
│       │   │   ├── DownloadQueue.js
│       │   │   └── Settings.js
│       │   └── index.css       # Tailwind CSS styles
│       ├── public/
│       │   └── index.html      # HTML template
│       ├── package.json        # Node.js dependencies
│       └── tailwind.config.js  # Tailwind configuration
├── Dockerfile                  # Multi-stage Docker build
├── docker-compose.yml         # Docker Compose configuration
└── .dockerignore              # Docker ignore rules
```

## ⚙️ Configuration

### Environment Variables
These can be overridden in `docker-compose.yml` or via an `.env` file:

| Variable | Default | Purpose |
| --- | --- | --- |
| `PUID` | `1000` | UID the container runs as; match to your host user for bind-mount ownership |
| `PGID` | `1000` | GID to run as |
| `XDG_CONFIG_HOME` | `/config` | tidal-dl-ng reads/writes `settings.json` and `token.json` under this path |
| `DOWNLOAD_PATH` | `/downloads` | Default `download_base_path` when settings are first created |
| `TIDDL_PATH` | `/config/tiddl` | tiddl engine stores mirrored `config.toml` + `auth.json` here (do not change unless you know why) |
| `ACTIVE_ENGINE` | _(unset)_ | Optional boot default: `tidal-dl-ng` or `tiddl`. When set, overrides the saved **Download engine** until you change it in Settings. |

### Docker Volumes
Two bind mounts persist state on the host so **nothing is lost on image rebuild**:

| Host path | Container path | Contents |
| --- | --- | --- |
| `./config` | `/config` | **Unified** `unified/settings.json` + `unified/auth.json` (canonical), mirrored copies for each engine: `tidal_dl_ng/settings.json`, `tidal_dl_ng/token.json`, `tiddl/config.toml`, `tiddl/auth.json` |
| `./downloads` | `/downloads` | Downloaded media files |

On first boot, an entrypoint script remaps the container's `app` user to the
requested `PUID:PGID` and chowns `/config` so tidal-dl-ng can write its config.
Your TIDAL OAuth token survives container recreation and `docker compose down`
because it lives in `./config/tidal_dl_ng/token.json` on the host.

### Settings
Access settings via the gear icon in the top-right corner:

#### Download Settings
- **Download engine**: `tidal-dl-ng` (default, full library + mixes via tidalapi) or `tiddl` (experimental; favorites-based playlists, no user mixes list). OAuth tokens are mirrored so you can switch without logging in again.
- **Download Path**: Where files are saved
- **Audio Quality**: Low (96kbps) to Hi-Res Lossless (24-bit/192kHz)
- **Video Quality**: 360p to 1080p
- **Skip Existing**: Avoid re-downloading existing files
- **Download Delay**: Add delays to mimic human behavior

#### Metadata Options
- **Embed Lyrics**: Include lyrics in audio files
- **Save Lyrics**: Create separate .lrc files
- **Extract FLAC**: Convert MP4 to FLAC (requires FFmpeg)

## 🎯 Usage Guide

### 1. Search for Music
- Use the search bar to find tracks, albums, artists, or playlists
- Select search type from the dropdown
- Click on results to add them to your download queue

### 2. Browse Your Library
- Your TIDAL playlists, mixes, and favorites appear in the left sidebar
- Click on any item to view its contents
- Use the refresh button to update your library

### 3. Manage Downloads
- View your download queue in the right panel
- Click "Start" to begin downloading
- Monitor progress with real-time status updates
- Clear completed downloads as needed

### 4. Quality Settings
- Configure audio quality up to Hi-Res Lossless (requires TIDAL HiFi Plus)
- Set video quality preferences
- Enable FLAC extraction for lossless audio

## 🔧 Development

### Frontend Development
```bash
cd web/frontend
npm start
```
This starts the React development server with hot reload.

### Backend Development
```bash
cd web/backend
python main.py
```
Or use the Docker development profile:
```bash
docker-compose --profile dev up
```

### Building for Production
```bash
# Build frontend
cd web/frontend
npm run build

# Build Docker image
docker build -t tidal-dl-pro .
```

## 🐛 Troubleshooting

### Common Issues

#### "Failed to initialize application"
- Check that all Python dependencies are installed
- Ensure the original `tidal_dl_ng` module is accessible

#### "Authentication failed"
- Clear browser cache and cookies
- Try logging out and back in
- Check that your TIDAL subscription is active

#### "Downloads not starting"
- Verify download path permissions
- Check available disk space
- Ensure TIDAL authentication is valid

#### "FLAC extraction failed"
- Install FFmpeg: `apt-get install ffmpeg` (Linux) or `brew install ffmpeg` (macOS)
- Verify FFmpeg is in your system PATH

### Docker Issues

#### Container won't start
```bash
# Check logs
docker compose logs tidal-dl-pro-web

# Rebuild if needed
docker-compose build --no-cache
```

#### Permission issues
If bind-mounted files end up owned by an unexpected UID, set `PUID`/`PGID` in
`docker-compose.yml` to match your host user (`id -u` / `id -g`) and restart
the container. The entrypoint will re-chown `./config` on the next start.

#### "No supported WebSocket library detected" / `/ws` returns 404
This means the `websockets`/`wsproto` packages were not installed into the
image. Rebuild with `docker compose build --no-cache`; the Dockerfile pins
`uvicorn[standard]` (quoted so the shell doesn't treat `[standard]` as a glob).

## 🔒 Security Considerations

- **TIDAL tokens** are stored securely in the config directory
- **Local network access** - the web interface runs on localhost by default
- **No external dependencies** for core functionality
- **Volume mounting** keeps sensitive data local

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/web-enhancement`
3. Make your changes to the web interface components
4. Test thoroughly with Docker Compose
5. Submit a pull request

## 📝 API Documentation

The FastAPI backend provides a comprehensive API. When running, visit:
```
http://localhost:8000/docs
```
for interactive API documentation.

### Key Endpoints
- `GET /api/status` - Application status
- `POST /api/auth/login` - Initiate TIDAL login
- `POST /api/search` - Search TIDAL content
- `GET /api/user-lists` - Get user's playlists/favorites
- `POST /api/download` - Add items to download queue
- `GET /api/settings` - Get/update application settings
- `WebSocket /ws` - Real-time updates

## 📜 License

This web interface extends the [tidal-dl-ng](https://github.com/exislow/tidal-dl-ng) project and follows the same license terms.

## 🙏 Acknowledgments

- Built on top of the excellent [TIDAL Downloader NG](https://github.com/exislow/tidal-dl-ng) by @exislow
- UI components powered by [Tailwind CSS](https://tailwindcss.com)
- Icons by [Heroicons](https://heroicons.com)
- Backend API built with [FastAPI](https://fastapi.tiangolo.com)

---

**Note**: This is an unofficial web interface for educational purposes. Please respect TIDAL's terms of service and only download content you have the right to access. 