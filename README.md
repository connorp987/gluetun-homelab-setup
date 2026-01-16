# 🎬 Gluetun Homelab Media Stack

> **Automated media management with VPN protection - add shows/movies, they download and organize automatically**

## Why This Stack?

- **🔐 Secure** - All downloads through VPN with kill switch
- **🤖 Automated** - Add media → auto-downloads → auto-organizes → watch
- **⚡ Fast** - Usenet maxes out your connection, GPU transcoding for smooth playback
- **📦 Complete** - Everything you need in one Docker Compose file

## 🛠️ What's Included

| Service | Purpose | Why It's Essential |
|---------|---------|-------------------|
| **Gluetun** | VPN Client | Routes all traffic through PIA, kill switch protection |
| **NZBGet** | Usenet Downloader | Fast, efficient, battle-tested downloader |
| **Prowlarr** | Indexer Manager | One place to manage all indexers, auto-syncs to Sonarr/Radarr |
| **Sonarr** | TV Show Manager | Automatically finds, downloads, and organizes TV shows |
| **Radarr** | Movie Manager | Automatically finds, downloads, and organizes movies |
| **Jellyfin** | Media Server | Beautiful, open-source alternative to Plex (no premium fees!) |
| **Homepage** | Dashboard | Unified interface for all your services |

## 📋 Service URLs

| Service | URL | Default Credentials |
|---------|-----|---------------------|
| NZBGet | http://192.168.50.30:6789 | nzbget / tegbzn6789 | (change in settings)
| Prowlarr | http://192.168.50.30:9696 | No login required |
| Sonarr | http://192.168.50.30:8989 | No login required |
| Radarr | http://192.168.50.30:7878 | No login required |
| Jellyfin | http://192.168.50.30:8096 | Setup wizard on first visit |
| Homepage | http://192.168.50.30:3000 | Dashboard |

---

## 🚀 Step-by-Step Setup

### **Step 1: Configure NZBGet (Usenet Downloader)**

1. Go to http://192.168.50.30:6789
2. Login with: `nzbget` / `tegbzn6789`
3. Click **Settings** (gear icon)
4. **NEWS-SERVERS** section:
   - Add your Usenet provider details:
     - Host: (e.g., news.usenetserver.com)
     - Port: 563 (SSL) or 119 (non-SSL)
     - Username: Your Usenet account username
     - Password: Your Usenet account password
     - Connections: 20-30 (depends on your provider)
     - Encryption: Yes
5. **PATHS** section:
   - MainDir: `/downloads` (already set)
   - DestDir: `/downloads/completed` 
   - InterDir: `/downloads/intermediate`
6. Click **Save all changes** at bottom
7. Reload NZBGet

**Don't have Usenet?** Popular providers:
- Newshosting
- UsenetServer
- Eweka
- Frugal Usenet

---

### **Step 2: Configure Prowlarr (Indexer Manager)**

Prowlarr manages all your indexers in one place and syncs them to Sonarr/Radarr.

1. Go to http://192.168.50.30:9696
2. Click **Settings** → **General**
   - Set authentication if desired
3. Click **Indexers** → **Add Indexer**
4. Add NZB indexers (examples):
   - **NZBgeek** (paid, highly recommended)
   - **NZBFinder** (free tier available)
   - **DrunkenSlug** (registration required)
   - Search for your preferred indexers
5. For each indexer:
   - Enter API key from the indexer's website
   - Test the connection
   - Save
6. Click **Settings** → **Apps**
7. Add Sonarr:
   - Click **+** → **Sonarr**
   - Prowlarr Server: `http://localhost:9696`
   - Sonarr Server: `http://localhost:8989`
   - API Key: (get from Sonarr → Settings → General)
   - Sync Level: Full Sync
   - Test & Save
8. Add Radarr:
   - Click **+** → **Radarr**
   - Prowlarr Server: `http://localhost:9696`
   - Radarr Server: `http://localhost:7878`
   - API Key: (get from Radarr → Settings → General)
   - Sync Level: Full Sync
   - Test & Save

**Prowlarr will now automatically sync all indexers to Sonarr and Radarr!**

---

### **Step 3: Configure Sonarr (TV Shows)**

1. Go to http://192.168.50.30:8989
2. Complete initial setup wizard
3. **Settings** → **Media Management**
   - Enable: Rename Episodes
   - Standard Episode Format: `{Series Title} - S{season:00}E{episode:00} - {Episode Title}`
   - Season Folder Format: `Season {season:00}`
4. **Settings** → **Download Clients**
   - Click **+** → **NZBGet**
     - Name: NZBGet
     - Host: `localhost`
     - Port: `6789`
     - Username: `nzbget`
     - Password: `tegbzn6789`
     - Category: `tv` (optional)
     - Test & Save
5. **Settings** → **Indexers**
   - Should auto-populate from Prowlarr
   - If not, go back to Prowlarr and sync
6. **Series** → **Add New**
   - Add your TV shows
   - Root Folder: `/tv`
   - Quality Profile: Any
   - Monitor: All Episodes
   - Search for missing episodes: Yes

---

### **Step 4: Configure Radarr (Movies)**

1. Go to http://192.168.50.30:7878
2. Complete initial setup wizard
3. **Settings** → **Media Management**
   - Enable: Rename Movies
   - Standard Movie Format: `{Movie Title} ({Release Year})`
4. **Settings** → **Download Clients**
   - Click **+** → **NZBGet**
     - Name: NZBGet
     - Host: `localhost`
     - Port: `6789`
     - Username: `nzbget`
     - Password: `tegbzn6789`
     - Category: `movies` (optional)
     - Test & Save
5. **Settings** → **Indexers**
   - Should auto-populate from Prowlarr
   - If not, go back to Prowlarr and sync
6. **Movies** → **Add New**
   - Add your movies
   - Root Folder: `/movies`
   - Quality Profile: Any
   - Search for movie: Yes

---

### **Step 5: Configure Jellyfin (Media Server)**

1. Go to http://192.168.50.30:8096
2. Complete setup wizard:
   - Set username and password
   - Add Media Libraries:
     - **TV Shows**: `/tv` (Type: Shows)
     - **Movies**: `/movies` (Type: Movies)
3. **Settings** → **Networking**
   - Enable automatic port mapping: No (not needed)
4. **Settings** → **Playback**
   - Configure transcoding if needed

---

## 🎬 How It Works (Automated Workflow)

```
1. You add a TV show to Sonarr or movie to Radarr
                    ↓
2. Sonarr/Radarr searches Prowlarr indexers for NZB files
                    ↓
3. NZB is sent to NZBGet for download (through VPN)
                    ↓
4. NZBGet downloads from Usenet (encrypted, through VPN)
                    ↓
5. When complete, Sonarr/Radarr moves file to media folder
                    ↓
6. Jellyfin automatically detects new media
                    ↓
7. Watch on Jellyfin!
```

---

## 🔒 Security Features

✅ **All download traffic encrypted through PIA VPN**
- NZBGet downloads through VPN
- Prowlarr searches through VPN
- If VPN drops, all traffic stops (kill switch)

✅ **Usenet is inherently secure**
- Encrypted connections (SSL)
- No peer-to-peer exposure
- No upload requirements
- Much faster than torrents

✅ **No port forwarding needed**
- All services behind VPN
- Access via local network or Nginx reverse proxy

---

## 📁 Directory Structure

```
/home/connor/docker-compose/glueton/
├── nzbget/
│   ├── config/              # NZBGet configuration
│   └── downloads/
│       ├── completed/       # Finished downloads
│       ├── intermediate/    # Processing
│       └── nzb/            # NZB files
├── sonarr/
│   ├── data/               # Sonarr config
│   └── tvseries/           # TV shows (shared with Jellyfin)
├── radarr/
│   ├── data/               # Radarr config
│   └── movies/             # Movies (shared with Jellyfin)
└── jellyfin/
    ├── config/             # Jellyfin config
    └── cache/              # Jellyfin cache
```

---

## 🚨 Troubleshooting

**Jellyfin not showing media?**
- Scan library manually
- Check file permissions (should be owned by user 1000:1000)
- Verify paths: `/tv` and `/movies`

**VPN not working?**
- Check PIA credentials in .env file
- View logs: `sudo docker compose logs gluetun`
- Verify VPN IP: `sudo docker exec nzbget curl -s ifconfig.me`

---

## 📚 Additional Resources

- **Usenet Guide**: https://www.reddit.com/r/usenet/wiki/
- **Prowlarr Wiki**: https://wiki.servarr.com/prowlarr
- **Sonarr Wiki**: https://wiki.servarr.com/sonarr
- **Radarr Wiki**: https://wiki.servarr.com/radarr
- **Jellyfin Docs**: https://jellyfin.org/docs/

---

## 🎉 You're All Set!

Your complete automated media system is ready:
- **Secure**: All downloads through VPN
- **Automated**: Add shows/movies, they download automatically
- **Fast**: Usenet is much faster than torrents
- **Private**: No peer-to-peer, no upload requirements
- **Easy**: Jellyfin provides Netflix-like experience

Enjoy your media! 🍿
