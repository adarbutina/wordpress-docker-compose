# WordPress Docker

A simple, production-ready Docker Compose setup for WordPress development with MySQL and phpMyAdmin.

## What's Included

- **WordPress** (latest) — running on port 8080
- **MySQL 8.0** — persistent database with healthcheck
- **phpMyAdmin** — database management UI on port 8081

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/) (included with Docker Desktop)

## Quick Start

1. **Configure environment variables** (optional but recommended)

The `.env` file already has working defaults. For any non-throwaway use, change the passwords:

```bash
cp .env.example .env
# then edit .env and set strong passwords
```

2. **Start the containers**

```bash
docker compose up -d
```

3. **Access the services**
   - WordPress: http://localhost:8080
   - phpMyAdmin: http://localhost:8081

4. **Complete WordPress installation**

On first visit to http://localhost:8080, WordPress will guide you through the standard setup (site title, admin user, password, etc.).

## Common Commands

```bash
# Start in background
docker compose up -d

# View logs (all services)
docker compose logs -f

# View logs for one service
docker compose logs -f wordpress

# Stop containers (data preserved)
docker compose down

# Stop AND delete all data (destructive)
docker compose down -v

# Restart a single service
docker compose restart wordpress

# Open a shell inside the WordPress container
docker compose exec wordpress bash
```

## Project Structure

```
wordpress-docker/
├── docker-compose.yml    # Service definitions
├── .env                  # Your config (not committed)
├── .env.example          # Template for .env
├── .gitignore
├── README.md
└── wp-content/           # Themes, plugins, uploads (mounted into container)
```

The `wp-content` directory is bind-mounted, so themes and plugins you drop in there appear immediately in WordPress. The rest of the WordPress core lives in a named volume (`wordpress_data`).

## Customization

- **Change ports**: edit `WORDPRESS_PORT` and `PHPMYADMIN_PORT` in `.env`
- **Change WordPress version**: replace `wordpress:latest` in `docker-compose.yml` with e.g. `wordpress:6.4-php8.2`
- **Increase upload size**: adjust `UPLOAD_LIMIT` for phpMyAdmin; for WordPress itself, mount a custom `uploads.ini` into `/usr/local/etc/php/conf.d/`

## Troubleshooting

**WordPress can't connect to the database**
The `db` service has a healthcheck and `wordpress` waits for it, but if you see this error, give it 10–20 seconds on first start, then `docker compose restart wordpress`.

**Port already in use**
Change `WORDPRESS_PORT` or `PHPMYADMIN_PORT` in `.env` to a free port.

**Reset everything**

```bash
docker compose down -v
docker compose up -d
```

This deletes all WordPress data including the database.

## Security Notes

This setup is intended for local development. Before using anywhere reachable from the internet:

- Set strong, unique passwords in `.env`
- Set `WORDPRESS_DEBUG=0`
- Remove or firewall the phpMyAdmin service
- Put WordPress behind HTTPS (e.g. a reverse proxy like Caddy or Traefik)
