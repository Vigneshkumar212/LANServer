# quickShare

A self-hosted file drop for your home network. Drag files in from your phone or laptop, they land on a server you control — no cloud, no accounts.

- Chunked uploads (handles large files without choking the browser or server)
- Automatic video compression and audio extraction via `ffmpeg`
- Lightweight vanilla JS/HTML frontend, no build step
- Single Node/Express process, easy to run on a Raspberry Pi, NAS, or spare machine

## Getting started

```bash
npm install
npm start
```

The server listens on `http://localhost:3000` by default. Set a `PORT` environment variable to change it:

```bash
PORT=8080 npm start
```

Open the printed URL from any device on the same network to upload and browse files.

## ⚠️ Security notice

This is built for a **trusted local network**, not the public internet. There is currently no authentication — anyone who can reach the server can upload and download files. Do not port-forward this or expose it directly to the internet without adding your own auth layer (reverse proxy with basic auth, VPN/Tailscale, etc.).

## How it works

- The frontend uploads files in chunks to `POST /upload`, tagging each chunk with an upload UUID so the server can reassemble it.
- Uploaded files are stored in `app/uploads/`; metadata (original filename, size, type, timestamps) is tracked server-side and exposed read-only via `GET /filesuploaded`.
- `GET /makeCompressedVideoServerFilename` and `GET /makeAudioExtractedServerFilename` kick off an `ffmpeg` job against an uploaded video and write the result alongside the original.

## Requirements

- Node.js
- No system `ffmpeg` install needed — `ffmpeg-static` bundles a binary for you.
