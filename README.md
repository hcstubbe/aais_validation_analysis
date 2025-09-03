# Data analysis for LLM tuning and validation

## Summary
This repository contains the analysis workflow for outputs produced by the AAIS‑validation pipeline during LLM tuning and validation. It focuses on consolidating raw evaluation artefacts (accuracy/quality metrics, safety signals, latency, cost KPIs, and custom domain indicators), transforming them into tidy analytical datasets, and generating comparative and publication‑ready visualizations. The primary entry point is the R Markdown document `data_analysis_and_plotting.Rmd`, which orchestrates data loading from `data/`, performs aggregation & statistical summaries, and writes figures to `figures/`. The included Docker / RStudio setup ensures a fully reproducible environment so results can be regenerated consistently across machines.

## Run locally via Docker (RStudio Server)

This project includes a simple Docker setup (`dev-stack.yml`) to launch an RStudio Server environment using the public image `rocker/rstudio:4.2.1`.

### Prerequisites
* Docker (Engine) and Docker Compose v2 (usually bundled with recent Docker installs)
* A free local TCP port `9090` (mapped to container port `8787`)

### Quick start (recommended)
From the repository root:

```bash
# 1. (Optional) Choose a password instead of the default 'test123'
export RSTUDIO_PASSWORD="changeme123"

# 2. Set the path to mount inside the container as the RStudio home directory
export DOCKER_DATA_PATH="$(pwd)"

# 3. Start the stack
docker compose -f dev-stack.yml up -d

# 4. Check logs (optional)
docker compose -f dev-stack.yml logs -f rstudio
```

Then open: http://localhost:9090

Login credentials:
* Username: `rstudio`
* Password: value of `$RSTUDIO_PASSWORD` (or `test123` if you didn't override)

Your repository files will appear directly under `/home/rstudio` in the RStudio file pane.

### Alternative: one-off run without Compose
```bash
docker run -d \
	--name aais-rstudio \
	-e PASSWORD="changeme123" \
	-p 9090:8787 \
	-v "$(pwd)":/home/rstudio \
	rocker/rstudio:4.2.1
```

### Stopping & cleanup
```bash
# Stop (Compose)
docker compose -f dev-stack.yml down

# Or for the single container variant
docker rm -f aais-rstudio
```

### Customization
Edit `dev-stack.yml` if you need to:
* Change the host port (`9090:8787`)
* Set a different password (override `PASSWORD`)
* Point `DOCKER_DATA_PATH` to a different host directory containing data

### Troubleshooting
* Port already in use: change the left side of the `ports` mapping (e.g. `8788:8787`).
* Blank page / cannot connect: ensure the container is healthy: `docker ps` then `docker logs <container>`.
* File permission issues: verify the mounted directory is writable by your user (the container runs as user `rstudio`, typically UID 1000). You can adjust ownership on the host: `chown -R $(id -u):$(id -g) .`.

### Security note
The default password `test123` is insecure. Always override it before exposing the service beyond your local machine.

## License
The code contains GPL-3 derived code from https://stackoverflow.com/questions/68095243/piedonut-how-to-change-color-of-pie-and-donut based on https://cardiomoon.github.io/webr/index.html (GPL-3!)
Therefore, this repo, specifically the code in the relevant sction, is licensed under GPL-3.
