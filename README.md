# hAI.FileBrowser

Portainer-Stack für [hurlenko/filebrowser-docker](https://github.com/hurlenko/filebrowser-docker) — ein schlanker, web-basierter Dateimanager im Docker-Container, eingebunden ins `highfishNetwork`.

[![Docker Pulls](https://img.shields.io/docker/pulls/hurlenko/filebrowser?logo=docker&color=0db7ed)](https://hub.docker.com/r/hurlenko/filebrowser)
[![Docker Image Size](https://img.shields.io/docker/image-size/hurlenko/filebrowser/latest?logo=docker)](https://hub.docker.com/r/hurlenko/filebrowser)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Portainer](https://img.shields.io/badge/Portainer-Stack-13BEF9?logo=portainer&logoColor=white)](https://www.portainer.io/)
[![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-Live-success?logo=github)](https://jbkunama1.github.io/hAI.FileBrowser/)
[![Last Commit](https://img.shields.io/github/last-commit/jbkunama1/hAI.FileBrowser)](https://github.com/jbkunama1/hAI.FileBrowser/commits/main)

## Übersicht

| Eigenschaft | Wert |
|---|---|
| Image | `hurlenko/filebrowser:latest` |
| Netzwerk | `highfishNetwork` (external) |
| Host | `192.168.178.21` |
| URL | [filebrowser.arbeitermili.eu](http://filebrowser.arbeitermili.eu) |
| Deployment | Portainer Git-Stack (Auto-Update via Pull/Webhook) |

## Struktur

```text
hAI.FileBrowser/
├── stacks/
│   └── filebrowser/
│       └── docker-compose.yml
├── docs/
│   └── index.html        # GitHub Pages Übersicht
└── README.md
```

## Portainer Setup

1. Portainer → **Stacks** → **Add Stack** → **Repository**
2. Repository URL: `https://github.com/<dein-user>/hAI.FileBrowser`
3. Reference: `refs/heads/main`
4. Compose path: `stacks/filebrowser/docker-compose.yml`
5. Optional: **GitOps updates** aktivieren (Polling oder Webhook), damit Änderungen aus dem Repo automatisch übernommen werden.

## Volumes

- `/srv/filebrowser/data` → `/data`
- `/srv/filebrowser/config` → `/config`

## Update-Workflow

```bash
git pull
# Änderungen in stacks/filebrowser/docker-compose.yml vornehmen
git commit -am "update stack"
git push
```

Portainer zieht die Änderung beim nächsten Poll-Intervall oder per Webhook-Trigger automatisch nach.

## Lizenz

Dieses Repository steht unter der [MIT-Lizenz](LICENSE). Das zugrunde liegende Image [hurlenko/filebrowser-docker](https://github.com/hurlenko/filebrowser-docker) unterliegt dessen eigener Lizenz.
