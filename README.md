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
| Port | `8888` (Host) → `8080` (Container) |
| URL | [filebrowser.arbeitermili.eu](http://filebrowser.arbeitermili.eu) |
| Deployment | Portainer Git-Stack (Auto-Update via Pull/Webhook) |

## Struktur

```text
hAI.FileBrowser/
├── docker-compose.yml
├── .env.example
├── docs/
│   └── index.html        # GitHub Pages Übersicht
└── README.md
```

## Portainer Setup

1. Portainer → **Stacks** → **Add Stack** → **Repository**
2. Repository URL: `https://github.com/<dein-user>/hAI.FileBrowser`
3. Reference: `refs/heads/main`
4. Compose path: `docker-compose.yml`
5. Optional: **GitOps updates** aktivieren (Polling oder Webhook), damit Änderungen aus dem Repo automatisch übernommen werden.

## Volumes

- `/srv/filebrowser/data` → `/data`
- `/srv/filebrowser/config` → `/config`

## Authentifizierung

File Browser startet standardmäßig mit dem Login `admin`/`admin` — dieses Passwort **muss** vor dem produktiven Einsatz geändert werden.

### Eigenen Benutzer beim ersten Start setzen

In der `.env` werden `FB_USERNAME` und `FB_PASSWORD` gesetzt:

```env
FB_USERNAME=daniel
FB_PASSWORD=BITTE_SICHERES_PASSWORT_EINTRAGEN
```

Diese Werte werden über die Compose-Datei als Umgebungsvariablen an den Container übergeben:

```yaml
environment:
  - FB_USERNAME=${FB_USERNAME}
  - FB_PASSWORD=${FB_PASSWORD}
```

**Wichtig:** Diese Variablen wirken nur, solange noch keine `filebrowser.db` im `/config`-Volume existiert. Wurde der Container bereits einmal gestartet, hat sich die Datenbank bereits mit den Default-Werten initialisiert — in dem Fall entweder:

- die Zugangsdaten direkt im Webinterface unter **Settings → Profile → Change Password** ändern, oder
- die Datei `filebrowser.db` im gemounteten Config-Verzeichnis (`FB_CONFIG_PATH`) löschen und den Container neu starten, damit die `.env`-Werte greifen.

### In Portainer eintragen

Wie bei den Verzeichnis-Variablen gilt: Portainer liest die `.env` bei Git-Stacks nicht automatisch mit. Trage `FB_USERNAME` und `FB_PASSWORD` daher direkt im Stack unter **Environment variables** ein (Advanced mode zum Einfügen im `KEY=VALUE`-Format).

### Zusätzliche Absicherung (optional)

Für einen weiteren Schutz nach außen (z. B. wenn die Subdomain öffentlich erreichbar ist), empfiehlt sich zusätzlich Basic Auth oder eine Zugriffsbeschränkung auf Reverse-Proxy-Ebene (z. B. IP-Whitelist oder Auth-Middleware in Traefik/Nginx Proxy Manager), damit selbst bei kompromittierten Zugangsdaten eine zweite Hürde besteht.

### Kein Login gewünscht (nur für vertrauenswürdige LAN-Umgebungen)

File Browser unterstützt auch einen Modus ganz ohne Login über `FB_NOAUTH=true`. Das wird **nicht empfohlen**, sobald die Instanz über eine öffentlich erreichbare Domain wie `filebrowser.arbeitermili.eu` läuft, da dann jeder mit Netzwerkzugriff auf deine Dateien zugreifen könnte.

## Update-Workflow

```bash
git pull
# Änderungen in docker-compose.yml vornehmen
git commit -am "update stack"
git push
```

Portainer zieht die Änderung beim nächsten Poll-Intervall oder per Webhook-Trigger automatisch nach.

## Lizenz

Dieses Repository steht unter der [MIT-Lizenz](LICENSE). Das zugrunde liegende Image [hurlenko/filebrowser-docker](https://github.com/hurlenko/filebrowser-docker) unterliegt dessen eigener Lizenz.


## Mehrere Verzeichnisse einbinden (per .env)

Die Verzeichnispfade werden nicht mehr hart in der `docker-compose.yml` hinterlegt, sondern über Umgebungsvariablen aus einer `.env`-Datei im selben Ordner (`.env`) eingelesen.

### 1. .env anlegen

Kopiere `.env.example` zu `.env` und trage deine echten Pfade ein:

```env
FB_CONFIG_PATH=/srv/filebrowser/config

FB_DATA_ALLGEMEIN=/srv/filebrowser/data
FB_DATA_DOKUMENTE=/mnt/nas/dokumente
FB_DATA_FOTOS=/mnt/nas/fotos
FB_DATA_PROJEKTE=/home/daniel/projekte

# FB_UID=1000
# FB_GID=1000
```

### 2. docker-compose.yml referenziert die Variablen

```yaml
volumes:
  - ${FB_CONFIG_PATH}:/config
  - ${FB_DATA_ALLGEMEIN}:/data/allgemein
  - ${FB_DATA_DOKUMENTE}:/data/dokumente
  - ${FB_DATA_FOTOS}:/data/fotos
  - ${FB_DATA_PROJEKTE}:/data/projekte
```

### 3. Wichtiger Hinweis für Portainer Git-Stacks

Portainer liest `.env`-Dateien bei Git-basierten Stacks **nicht automatisch aus dem Repository** — aus Sicherheitsgründen wird die `.env` nicht mitgeklont, falls sie ins Repo gepusht würde (sie sollte ohnehin nie ins Repo, siehe `.gitignore`). Trage die Variablen daher direkt in Portainer ein:

1. Beim Stack-Deployment (oder später unter **Editor**) den Abschnitt **Environment variables** öffnen.
2. Entweder einzeln per **Add environment variable** eintragen, oder über **Advanced mode** den Inhalt deiner `.env`-Datei im `KEY=VALUE`-Format einfügen.
3. Stack deployen bzw. **Update the stack** klicken.

### 4. Neue Verzeichnisse ergänzen

Um ein weiteres Verzeichnis hinzuzufügen, in der `docker-compose.yml` eine neue Zeile ergänzen:

```yaml
- ${FB_DATA_BACKUP}:/data/backup
```

und in Portainer die passende Umgebungsvariable `FB_DATA_BACKUP=/mnt/backup` hinzufügen. Für read-only-Freigaben `:ro` anhängen, z. B. `- ${FB_DATA_BACKUP}:/data/backup:ro`.

### 5. Rechteprobleme

Falls File Browser keinen Zugriff auf bestimmte Ordner hat, `user: "${FB_UID}:${FB_GID}"` in der Compose-Datei aktivieren und `FB_UID`/`FB_GID` mit `id daniel` auf dem Host ermitteln.
