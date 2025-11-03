# Nextcloud

Self-hosted cloud storage and collaboration platform.

## Features

- File sync and share
- Virtual files (on-demand download)
- Calendar, Contacts, Tasks
- Photo management
- Office documents
- Mobile apps (iOS/Android)
- Desktop sync clients

## Quick Start

```bash
# 1. Configure
cp env_template .env
nano .env

# 2. Start
docker compose up -d

# 3. Access
http://localhost:80  # Initial setup
```

## Initial Setup

1. Access the web interface
2. Create admin account (or it uses credentials from .env)
3. Install recommended apps
4. Configure external storage (optional)

## Configuration

### Environment Variables

- `NEXTCLOUD_DOMAIN` - Your domain (e.g., nextcloud.yourdomain.com)
- `NEXTCLOUD_ADMIN_USER` - Admin username
- `NEXTCLOUD_ADMIN_PASSWORD` - Admin password (change this!)
- `POSTGRES_PASSWORD` - Database password (change this!)

### External Storage

To mount your external drive into Nextcloud:

Add to `docker-compose.override.yaml`:
```yaml
services:
  nextcloud:
    volumes:
      - /media/bommel/syncthing:/external-data
```

Then in Nextcloud:
- Settings → External Storage
- Add local storage: `/external-data`

## Desktop Client

Download from: https://nextcloud.com/install/#install-clients

**Enable Virtual Files:**
1. Install desktop client
2. During setup, enable "Virtual Files" or "Files On-Demand"
3. Files appear locally but don't download until you open them

## Mobile Apps

- **iOS**: https://apps.apple.com/app/nextcloud/id1125420102
- **Android**: https://play.google.com/store/apps/details?id=com.nextcloud.client

## Maintenance

### Backup

```bash
# Backup volumes
docker run --rm \
  -v nextcloud_data:/data:ro \
  -v nextcloud_db:/db:ro \
  -v $(pwd)/backups:/backup \
  alpine \
  tar czf /backup/nextcloud_$(date +%Y%m%d).tar.gz -C / data db
```

### Updates

```bash
docker compose pull
docker compose up -d
```

### Occ Command

Run Nextcloud's occ command:
```bash
docker exec -u www-data nextcloud php occ <command>

# Examples:
docker exec -u www-data nextcloud php occ status
docker exec -u www-data nextcloud php occ files:scan --all
docker exec -u www-data nextcloud php occ maintenance:mode --on
```

## Troubleshooting

### Can't access via domain

Check trusted domains:
```bash
docker exec -u www-data nextcloud php occ config:system:set trusted_domains 1 --value=nextcloud.yourdomain.com
```

### Performance issues

Enable Redis caching (already configured in docker-compose):
```bash
docker exec -u www-data nextcloud php occ config:system:set redis host --value=nextcloud-redis
docker exec -u www-data nextcloud php occ config:system:set redis port --value=6379
```

### Reset admin password

```bash
docker exec -u www-data nextcloud php occ user:resetpassword admin
```

## Default Ports

- **80**: HTTP (accessed via Cloudflare Tunnel in self-hosting mode)

## Links

- [Nextcloud Documentation](https://docs.nextcloud.com/)
- [Nextcloud Apps](https://apps.nextcloud.com/)
- [Desktop Clients](https://nextcloud.com/install/#install-clients)
