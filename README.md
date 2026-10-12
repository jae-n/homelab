# Raspberry Pi Music Server

A self-hosted music streaming and discovery setup built with a Raspberry Pi, Docker, Navidrome, Explo, slskd, and two USB storage drives.

## Overview

This project provides a personal music library accessible from computers and mobile devices. It combines local music storage, music streaming, playlist management, and automated music discovery.

## Architecture

```text
                    Mobile / Desktop Clients
                              |
                         Tailscale / LAN
                              |
                         Navidrome
                              |
                  +-----------+-----------+
                  |                       |
             USB Drive 1             USB Drive 2
             /mnt/music              /mnt/music2
             Existing music          New music
                                         |
                                   Explo /data
                                         |
                                  Explo playlist jobs
                                         |
                                      slskd
                                         |
                                  Download staging
```

## Services

| Service   | Purpose                                 | Default Port |
| --------- | --------------------------------------- | -----------: |
| Navidrome | Music streaming and library management  |         4533 |
| Explo     | Playlist automation and music discovery |         7288 |
| slskd     | Soulseek search and download management |         5030 |

Ports are configurable and should not be exposed directly to the public internet without appropriate security controls.

## Storage Layout

### USB Drive 1

Mount point: `/mnt/music`

Contains existing music and the original music library.

### USB Drive 2

Mount point: `/mnt/music2`

Contains storage for new music and downloads.

```text
/mnt/music2/
├── slskd/
│   ├── downloads/
│   └── incomplete/
└── explo/
```

Verify the actual Docker mounts before relying on this layout. Incomplete downloads may remain in the Raspberry Pi's local filesystem unless explicitly mapped to the second drive.

## Technology

* Raspberry Pi OS
* Docker Compose
* Navidrome
* Explo
* slskd
* USB storage
* Tailscale for remote access

## Common Commands

### Start the services

Start Explo and slskd:

```bash
cd ~/explo
docker compose up -d
```

Start Navidrome:

```bash
cd ~/navidrome
docker compose up -d
```

### Check running containers

```bash
docker ps
```

### Check USB storage

```bash
df -h /mnt/music /mnt/music2
```

### Check service logs

```bash
docker logs --tail 50 explo
docker logs --tail 50 slskd
docker logs --tail 50 navidrome-navidrome-1
```

### Shut down safely

```bash
sudo shutdown -h now
```

Wait for the Raspberry Pi to finish shutting down before removing power.

## Configuration and Security

* Keep API tokens, passwords, and secret environment files outside the public repository.
* Use `.gitignore` to exclude private configuration and credential files.
* Do not commit personal music files or downloaded media.
* Prefer Tailscale or another secure access method for remote administration.
* Restrict access to the Explo and slskd web interfaces with authentication.
* Back up Docker configuration and application data separately from the music library.

## Troubleshooting

### A service is not responding

Check the container status:

```bash
docker ps
```

Inspect its logs:

```bash
docker logs --tail 50 CONTAINER_NAME
```

### A USB drive is missing

Check mounted filesystems:

```bash
lsblk -f
mount | grep /mnt/music
```

### A song does not appear

Check its file path, confirm the relevant USB drive is mounted, and run a Navidrome library scan.

## Backup and Recovery

Back up the following independently:

1. Docker Compose files and service configuration.
2. Navidrome application data and database.
3. Music stored on both USB drives.
4. A private copy of credentials and API tokens, stored securely outside GitHub.

A second USB drive is additional storage, not a backup by itself.

## Status

This is a personal home-lab project. Paths, ports, and configuration details should be reviewed against the current installation before reproducing the setup.
