# Bits per Channel

An interactive demo of color bit depth: mix red, green and blue at 1, 2, 4 or 8 bits per channel, and play a color-matching game.

This package is a **static website with one file**, `index.html`. There is no build step, no backend, no database and nothing to install. Any web server that can serve a file will host it.

## Contents

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: markup, styles and script in one file |
| `README.md` | These instructions |

## Hosting on Linux

Pick one of the options below. All commands assume the package was unpacked to `/opt/bits-per-channel` (so the page is at `/opt/bits-per-channel/index.html`). Change the path and port to suit.

```bash
sudo mkdir -p /opt/bits-per-channel
sudo unzip bits-per-channel.zip -d /opt
```

### Option A: quick test with Python (already on most Linux systems)

```bash
python3 -m http.server 8080 --directory /opt/bits-per-channel
```

Then open `http://<this-machine>:8080/`. This runs until the terminal is closed, so use option B or C to keep it up.

### Option B: keep it running with systemd

Create `/etc/systemd/system/bits-per-channel.service`:

```ini
[Unit]
Description=Bits per Channel static site
After=network.target

[Service]
ExecStart=/usr/bin/python3 -m http.server 8080 --directory /opt/bits-per-channel
Restart=on-failure
DynamicUser=yes

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now bits-per-channel
```

### Option C: nginx

Add a server block (for example `/etc/nginx/conf.d/bits-per-channel.conf`):

```nginx
server {
    listen 8080;
    server_name _;
    root /opt/bits-per-channel;
    index index.html;
}
```

```bash
sudo nginx -t && sudo systemctl reload nginx
```

### Firewall

If other machines need to reach it, open the port, for example:

```bash
sudo ufw allow 8080/tcp
```

## Checking it works

```bash
curl -sI http://localhost:8080/ | head -n 1
```

Expect `HTTP/1.0 200 OK` (Python) or `HTTP/1.1 200 OK` (nginx). In a browser the page should show a colored patch, a rolling list of bit depths, and four slider rows (Red, Green, Blue, Offset) on a dark background.

## Things to know

- **Fonts.** The page loads two typefaces (Geist and Geist Mono) from Google Fonts (`fonts.googleapis.com`, `fonts.gstatic.com`). If the viewer's browser has no internet access the page still works and falls back to the system fonts; it just looks slightly different. That is the only outside request the page makes.
- **No data leaves the browser.** The one setting (reset sliders after a match) is saved in the viewer's own browser with `localStorage`. Nothing is sent to the server.
- **It can also be opened directly.** Double-clicking `index.html` works without any server.
- **Browsers.** Needs a current browser (Chrome, Edge, Firefox or Safari from 2023 or later).
- **Subpath hosting.** The page uses no absolute paths, so it can be served from a subfolder such as `/demos/bits-per-channel/` without changes.
