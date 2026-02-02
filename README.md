# Ghost + MySQL Docker Template

This template repository provides a Docker Compose setup for a Ghost 5 blog with a MySQL 8 database. It is intentionally **template-ified** with placeholders like `[fill-me-in]` that you must replace when you create your own instance.

## 1. Required replacements

There are two key places where you must replace `[fill-me-in]`:

1. **Makefile**

   At the top of `Makefile`:

   ```makefile
   INSTANCE_NAME = [fill-me-in]
   ```

   Replace `[fill-me-in]` with a short, unique name for this instance, for example:

   ```makefile
   INSTANCE_NAME = family-blog
   ```

2. **docker-compose.yml**

   In `docker-compose.yml`, replace all occurrences of `[fill-me-in]` with the same value you used for `INSTANCE_NAME`.

   Example: if you choose `family-blog`:

   ```yaml
   services:
     family-blog-ghost:
       ...
       depends_on:
         - family-blog-db
       environment:
         database__connection__host: family-blog-db
       ...

     family-blog-db:
       ...
   ```

   The service names and the `database__connection__host` **must match** so Ghost can reach the correct MySQL container.

> Important: The value you use instead of `[fill-me-in]` must be identical in:
>
> - `INSTANCE_NAME` in `Makefile`
> - `*-ghost` and `*-db` service names in `docker-compose.yml`
> - `database__connection__host` in the Ghost service.

## 2. Configure environment variables

Copy the example environment file:

```bash
cp .env.example .env
```

Edit `.env` and set real values:

- `GHOST_URL` – URL where your blog will be accessible (e.g. `http://localhost:8080` in development).
- `MYSQL_ROOT_PASSWORD` – strong root password.
- `MYSQL_USER`, `MYSQL_PASSWORD`, `MYSQL_DATABASE` – credentials Ghost will use for MySQL.

Keep `.env` out of version control (already covered by `.gitignore`).

## 3. Prepare local directories

Create directories that will be used as volumes:

```bash
mkdir -p content/themes
mkdir -p themes
mkdir -p data/mysql
```

These will persist Ghost content, themes, and database data between container restarts.

## 4. Start the project

After you have:

- Replaced `[fill-me-in]` in `Makefile` and `docker-compose.yml`.
- Configured `.env` with your own values.

You can start the stack with the Makefile:

```bash
make up
```

Or directly with Docker Compose:

```bash
docker compose up -d
```

Once the containers are running, open:

- `http://localhost:8080` (or whatever host/port you configured) in your browser, and complete the Ghost setup wizard.

## 5. Makefile commands

The Makefile uses `INSTANCE_NAME` to target the correct services:

- `make db` – starts `[INSTANCE_NAME]-db` only.
- `make ghost` – starts `[INSTANCE_NAME]-ghost` only (assumes DB is up).
- `make up` – starts `[INSTANCE_NAME]-db`, waits for a keypress, then starts `[INSTANCE_NAME]-ghost`.
- `make down` – stops and removes all services in this compose file.
- `make rebuild` – rebuilds and starts services.
- `make logs` – follows logs for all services.
- `make ps` – shows container status.

Because the service names in `docker-compose.yml` include the same `[fill-me-in]` prefix, you can create multiple copies of this template (in different directories), each with a different `INSTANCE_NAME` and `[fill-me-in]` value, and run them side by side on the same host.

## 6. Customization tips

- Change the host port mapping (`"8080:2368"`) in `docker-compose.yml` if you want each instance on a different port.
- Integrate with a reverse proxy (e.g. Nginx or Traefik) if you want to serve multiple domains on ports 80/443.
- Pin specific image tags for Ghost and MySQL if you need controlled upgrades.
