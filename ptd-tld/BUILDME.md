# .ptd — Local TLD Manager for openSUSE Tumbleweed

A lightweight tool that creates a private `.ptd` top-level domain on your
localhost. Every `*.ptd` domain resolves to `127.0.0.1` and is served by nginx
from `/var/www/ptd/<project>/`.

## Architecture

| Component | Role |
|-----------|------|
| **dnsmasq** | Local DNS server on `127.0.0.1:53`; wildcards `*.ptd` → `127.0.0.1`, forwards everything else to your real DNS |
| **nginx**   | HTTP server on `:80`; wildcard `server_name` for `*.ptd` maps each subdomain to its directory |
| **`ptd` CLI** | Bash script that installs, manages projects, and tears everything down |

## Quick Start

```bash
# 1. Clone or copy the script
sudo install -m 755 ptd /usr/local/bin/ptd

# 2. Install everything (dnsmasq + nginx + DNS config)
sudo ptd install

# 3. Create a project
sudo ptd add myproject

# 4. Open in browser
firefox http://myproject.ptd/
```

## Usage

```
ptd <command> [<args>]

Commands:
    install          Set up dnsmasq + nginx + system DNS for .ptd
    uninstall        Remove all .ptd configuration and restore system DNS
    add <name>       Create a new .ptd project at /var/www/ptd/<name>/
    remove <name>    Delete a .ptd project
    list             List all .ptd projects
    status           Show service, DNS, and project status
    restart          Restart dnsmasq and nginx services
    help             Show this help message
```

## Step-by-Step

### 1. Prerequisites

- openSUSE Tumbleweed
- `sudo` access
- Port 53 (DNS) and port 80 (HTTP) must be free

### 2. Install the script

```bash
sudo install -m 755 /path/to/ptd /usr/local/bin/ptd
```

### 3. Run the installer

```bash
sudo ptd install
```

The installer will:

1. Install `dnsmasq` and `nginx` via `zypper` if missing
2. Detect your current upstream DNS servers (saves them for restore)
3. Write `/etc/dnsmasq.d/ptd.conf` — listens on `127.0.0.1:53`, resolves
   `*.ptd` → `127.0.0.1`, forwards everything else upstream
4. Configure your system DNS to use `127.0.0.1` (via `nmcli` or `netconfig`)
5. Write `/etc/nginx/conf.d/ptd.conf` — wildcard `server_name` for `*.ptd`
   with document root `/var/www/ptd/$subdomain`
6. Start and enable both services

### 4. Create a project

```bash
sudo ptd add myproject
```

This creates `/var/www/ptd/myproject/index.html` with a welcome page.
Open `http://myproject.ptd/` in any browser.

### 5. Manage projects

```bash
ptd list                     # List all projects
sudo ptd remove myproject    # Delete a project
sudo ptd restart             # Restart services (after manual edits)
```

### 6. Undo everything

```bash
sudo ptd uninstall
```

This stops services, removes config files, restores your original DNS
settings, and cleans up `/var/lib/ptd/`. It does **not** remove packages
or your project files in `/var/www/ptd/`.

## How It Works

### DNS Flow

```
Browser → http://myproject.ptd/
           ↓
    /etc/resolv.conf says "use 127.0.0.1"
           ↓
    dnsmasq (127.0.0.1:53)
      → matches /ptd/ wildcard → returns 127.0.0.1 (A record)
      → anything else → forwards to real upstream DNS
           ↓
    Browser connects to 127.0.0.1:80 with Host: myproject.ptd
           ↓
    nginx matches server_name regex ~^(?.+)\.ptd$
      → serves /var/www/ptd/myproject/
```

### File Layout

```
/var/www/ptd/                  # Root for all .ptd projects
├── index.html                 # Default page for http://ptd/
├── myproject/                 # Created by: ptd add myproject
│   └── index.html             # http://myproject.ptd/
└── another-project/
    └── index.html             # http://another-project.ptd/
```

## Advanced

### Adding custom DNS upstream servers

Edit `/etc/dnsmasq.d/ptd.conf` and add:

```
server=10.0.0.1
```

Then restart: `sudo ptd restart`

### Using a different web root

The web root is hardcoded in the nginx config. Edit
`/etc/nginx/conf.d/ptd.conf` and change the `root` directives, then run
`sudo ptd restart`.

### IPv6

The current setup is IPv4-only. If you need IPv6, add the following to
`/etc/dnsmasq.d/ptd.conf`:

```
address=/ptd/::1
listen-address=::1
```

And update the nginx config to `listen [::1]:80;`.

## Troubleshooting

**`test.ptd` doesn't resolve to 127.0.0.1**
```bash
dig test.ptd @127.0.0.1 +short
# Should print: 127.0.0.1
nslookup test.ptd 127.0.0.1
```

Check that dnsmasq is running:
```bash
systemctl status dnsmasq
journalctl -u dnsmasq -n 20 --no-pager
```

**`http://myproject.ptd/` returns connection refused**

Check that nginx is running and port 80 is open:
```bash
systemctl status nginx
ss -tlnp | grep :80
```

**DNS resolution for non-`.ptd` domains is broken**

Dnsmasq may have stale upstream servers. Run:
```bash
sudo ptd uninstall
sudo ptd install
```

**NetworkManager overrides my DNS**

After `ptd install`, if you reconnect to Wi-Fi or restart NetworkManager,
the DNS settings are re-applied from the connection profile. If they get
lost, re-run: `sudo ptd install` (it will skip already-installed packages).

## Uninstall

```bash
sudo ptd uninstall

# Optionally remove packages and project data:
sudo zypper rm dnsmasq nginx
sudo rm -rf /var/www/ptd
```

## License

MIT
