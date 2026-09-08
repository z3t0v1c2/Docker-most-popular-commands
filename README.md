# 🐳 Docker Command Reference

> A comprehensive reference for the `docker run`, `docker exec`, `docker build`, and `docker ps` commands and their most frequently used options.

## 📑 Contents

- [`docker run`](https://www.google.com/search?q=%23docker-run)
- [`docker exec`](https://www.google.com/search?q=%23docker-exec)
- [`docker build`](https://www.google.com/search?q=%23docker-build)
- [`docker ps`](https://www.google.com/search?q=%23docker-ps)
- [Quick Reference Table](https://www.google.com/search?q=%23quick-reference-table)
- [Related Commands](https://www.google.com/search?q=%23related-commands)

## `docker run`

**Creates and starts a new container** from the specified image. It does not restart an existing container—use `docker start` for that.

### Syntax

Bash

```
docker run [OPTIONS] IMAGE[:TAG] [COMMAND] [ARG...]

```

### Working Mode

| **Option**            | **Description**                                                                             |
| --------------------- | ------------------------------------------------------------------------------------------- |
| `-d`, `--detach`      | Runs the container in the background, prints its ID, and returns to the terminal            |
| `-i`, `--interactive` | Keeps STDIN open                                                                            |
| `-t`, `--tty`         | Allocates a pseudo-TTY                                                                      |
| `-it`                 | Combination of `-i` and `-t`; the standard way to open an interactive shell                   |
| `--rm`                | Automatically removes the container when it exits (leaves no disk clutter during test runs) |
| `-a`, `--attach`      | Attaches to STDIN/STDOUT/STDERR                                                             |

### Naming and Identity

| **Option**         | **Description**                                                         |
| ------------------ | ----------------------------------------------------------------------- |
| `--name`           | Assigns a custom name to the container instead of an auto-generated one |
| `-h`, `--hostname` | Sets the hostname inside the container                                  |
| `-l`, `--label`    | Adds metadata in `key=value` format                                     |

### Network

| **Option**            | **Description**                                                                                   |
| --------------------- | ------------------------------------------------------------------------------------------------- |
| `-p`, `--publish`     | Maps host\:container ports (e.g., `8080:80`)                                                      |
| `-P`, `--publish-all` | Exposes all `EXPOSE`d ports from the image to random host ports                                   |
| `--network`           | Connects the container to a specific network (`bridge`, `host`, `none`, or a custom network name) |
| `--add-host`          | Adds a host\:IP mapping to `/etc/hosts`                                                           |
| `--link`              | (Legacy) Links to another container; replaced by custom networks                                  |

### Storage

| **Option**       | **Description**                                                                           |
| ---------------- | ----------------------------------------------------------------------------------------- |
| `-v`, `--volume` | Mounts a host directory or named volume into the container (`/host/path:/container/path`) |
| `--mount`        | A more explicit syntax version of `-v` (`type=bind,source=...,target=...`)                  |

### Environment Variables

| **Option**    | **Description**                                            |
| ------------- | ---------------------------------------------------------- |
| `-e`, `--env` | Defines an environment variable (`-e NODE_ENV=production`) |
| `--env-file`  | Reads environment variables from a file                    |

### Resource Limits

| **Option**       | **Description**                     |
| ---------------- | ----------------------------------- |
| `-m`, `--memory` | Memory limit (e.g., `512m`)         |
| `--cpus`         | Number of usable CPUs (e.g., `1.5`) |
| `--gpus`         | Grants GPU access (`--gpus all`)    |

### Privileges and User

| **Option**                 | **Description**                                         |
| -------------------------- | ------------------------------------------------------- |
| `-u`, `--user`             | User/UID used to run commands inside the container           |
| `-w`, `--workdir`          | Working directory inside the container                  |
| `--privileged`             | Grants extended (host-like) privileges—use with caution |
| `--cap-add` / `--cap-drop` | Adds or drops specific kernel capabilities              |

### Restart and Other Options

| **Option**     | **Description**                                      |
| -------------- | ---------------------------------------------------- |
| `--restart`    | `no`, `on-failure`, `always`, `unless-stopped`       |
| `--entrypoint` | Overrides the image's default `ENTRYPOINT`           |
| `--pull`       | Controls when to pull the image: `always`, `missing`, `never` |

### Examples

Bash

```
# Start Nginx in the background, map port 8080 to 80
docker run -d -p 8080:80 --name web nginx

# Open an interactive bash inside Ubuntu, remove container upon exit
docker run -it --rm ubuntu bash

# Mount host directory into container, set working directory
docker run -v $(pwd):/app -w /app node:20 npm install

# Provide environment variable, apply restart policy
docker run -d -e POSTGRES_PASSWORD=secret --restart unless-stopped postgres

# Run with GPU access
docker run --gpus all -it nvidia/cuda:12.4.1-base-ubuntu24.04 nvidia-smi

```

## `docker exec`

Runs a new command inside an **already running** container. While `docker run` creates a new container, `exec` enters an existing one.

### Syntax

Bash

```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

```

### Options

| **Option**            | **Description**                                               |
| --------------------- | ------------------------------------------------------------- |
| `-i`, `--interactive` | Keeps STDIN open                                              |
| `-t`, `--tty`         | Allocates a pseudo-TTY                                        |
| `-it`                 | Both together; the standard way to enter a container with a shell |
| `-d`, `--detach`      | Runs the command in the background                            |
| `-u`, `--user`        | Runs the command with a specific user/UID                     |
| `-w`, `--workdir`     | Sets the working directory for the command                    |
| `-e`, `--env`         | Defines an additional environment variable                         |
| `--env-file`          | Reads environment variables from a file                       |
| `--privileged`        | Runs with extended privileges                                 |
| `--detach-keys`       | Customizes the key sequence used for detaching                     |

### Examples

Bash

```
# Enter a running container with an interactive shell
docker exec -it web bash

# Run a command with a specific user instead of root
docker exec -u 1000 -it web sh

# Run a command in a specific directory
docker exec -w /var/www web ls -la

# Run a one-off command in the background
docker exec -d web touch /tmp/ready.txt

```

> 💡 **Note:** `docker exec` requires the container to be **running**. You cannot enter a stopped container using `exec`; you must start it first with `docker start`.

## `docker build`

Produces a new **image** from a `Dockerfile`.

### Syntax

Bash

```
docker build [OPTIONS] PATH | URL | -

```

### Basic Options

| **Option**     | **Description**                                                      |
| -------------- | -------------------------------------------------------------------- |
| `-t`, `--tag`  | Names and tags the image (`-t app:1.0`) — can be used multiple times |
| `-f`, `--file` | Specifies a different file instead of the default `Dockerfile`       |

### Cache and Build Behavior

| **Option**    | **Description**                                           |
| ------------- | --------------------------------------------------------- |
| `--no-cache`  | Builds from scratch without using the build cache         |
| `--pull`      | Re-pulls the base image even if it is already up to date locally  |
| `--build-arg` | Sets `ARG` values in the Dockerfile at build time         |
| `--target`    | Specifies which stage to build in multi-stage Dockerfiles |
| `--progress`  | Progress output format: `auto`, `plain`, `tty`        |

### BuildKit / buildx Based Options

Available directly in modern Docker versions (BuildKit is the default builder):

| **Option**                    | **Description**                                                        |
| ----------------------------- | ---------------------------------------------------------------------- |
| `--platform`                  | Target platform(s) (`linux/amd64,linux/arm64`)                         |
| `-o`, `--output`              | Where to write the build output (local file system, tar, etc.)         |
| `--cache-from` / `--cache-to` | Remote/local build cache sources                                       |
| `--secret`                    | Passes a secret to the build process without embedding it in the image |
| `--ssh`                       | Forwards the SSH agent socket/keys to the build (for private repository clones)  |

### Other

| **Option**   | **Description**                            |
| ------------ | ------------------------------------------ |
| `--network`  | Network mode to use for `RUN` steps        |
| `--label`    | Adds metadata to the image                 |
| `--add-host` | Adds an entry to `/etc/hosts` during the build |

### Examples

Bash

```
# Simple build using the Dockerfile in the current directory
docker build -t myapp:1.0 .

# Build using a different Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build without cache, with a build argument
docker build --no-cache --build-arg NODE_ENV=production -t app:latest .

# Build only the "test" stage in a multi-stage Dockerfile
docker build --target test -t app:test .

# Build for multiple architectures and push to a registry
docker buildx build --platform linux/amd64,linux/arm64 -t username/app:1.0 --push .

```

> 💡 **Note:** When building for multiple architectures, `docker buildx build` is typically used and the result is sent directly to a registry via `--push`, because it is not possible to hold multiple architectures in a single local image at the same time.

## `docker ps`

Lists running containers (or all containers with `-a`).

### Syntax

Bash

```
docker ps [OPTIONS]

```

### Options

| **Option**       | **Description**                                                     |
| ---------------- | ------------------------------------------------------------------- |
| `-a`, `--all`    | Shows all containers, not just running ones, including stopped ones |
| `-q`, `--quiet`  | Prints only container IDs (ideal for scripts)                       |
| `-f`, `--filter` | Filters based on conditions (`status=running`, `name=web`, etc.)    |
| `-n`, `--last`   | Shows the last N created containers (including stopped ones)        |
| `-l`, `--latest` | Shows the latest created container                                  |
| `-s`, `--size`   | Displays the disk size of the containers                            |
| `--no-trunc`     | Shows output without truncation (full IDs, commands, etc.)          |
| `--format`       | Customizes output using Go template syntax                          |

### Examples

Bash

```
# Only running containers
docker ps

# All containers (including stopped ones)
docker ps -a

# List only IDs
docker ps -aq

# Filter by name
docker ps -f name=web

# Filter by status (e.g., exited containers)
docker ps -a -f status=exited

# Show with a custom table format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"

# Last 3 created containers
docker ps -n 3

```

## Quick Reference Table

| **Command**    | **What It Does**                              | **Most Common Usage**            |
| -------------- | --------------------------------------------- | -------------------------------- |
| `docker run`   | Creates and runs a new container              | `docker run -it --rm image bash` |
| `docker exec`  | Sends a command to / enters a running container | `docker exec -it container bash` |
| `docker build` | Produces an image from a Dockerfile           | `docker build -t name:tag .`     |
| `docker ps`    | Lists containers                              | `docker ps -a`                   |

## Related Commands

A few frequently used commands commonly used alongside `run` / `exec` / `build` / `ps`:

Bash

```
docker logs -f web       # Follow logs live
docker stop web          # Stop the container
docker start web         # Restart a stopped container
docker rm web            # Remove a stopped container
docker images            # List local images
docker rmi image:tag     # Remove an image

```
