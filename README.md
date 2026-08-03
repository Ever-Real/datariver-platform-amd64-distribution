# DataRiver AMD64 offline platform release

This repository intentionally publishes one current, closed-network `linux/amd64` platform
release. Large artifacts are stored in Git LFS.

## Release

`3c0379f4051e` — source commit `3c0379f4051ec698a53db41c5b5092895e40b8fb`

The Core archive contains the images required to run the DataRiver platform itself:

- PostgreSQL 17.10
- DataRiver backend image, including migrate, storage-init, bootstrap, API and workers
- DataRiver web image
- DataRiver Keycloak 26.7.0 image

Redis 8.2.6 is provided as a separate AMD64 image because the platform runs its cache and delivery
services through `compose.local-connectors.yaml`.

Neo4j 2026.06.0 is provided as a separately checksummed optional AMD64 image for a preparation host
that enables graph projection. The manifest records the exact upstream digest, image ID and
platform used to produce the archive.

pgvector 0.8.2 on PostgreSQL 17 is provided as a separately checksummed AMD64 image for the
preparation-PC source-host workflow. It is a third-party target prerequisite, not a DataRiver
application image. The archive has the exact tag expected by the source-host workflow:
`pgvector/pgvector:0.8.2-pg17-bookworm`.

The `datariver-uv-cache-linux-x86_64-*` archive contains the exact frozen Python dependency cache
for Linux AMD64, Python 3.12.12 and uv 0.9.17. It includes `pypdf==6.13.3` and was accepted only
after a clean `uv sync --frozen --all-extras --offline` verification in a Linux AMD64 container.

This release deliberately excludes other external or optional services: MinIO, DataHub, Airflow,
APISIX and observability services. They must be supplied by the selected target environment.
No environment file, secret, volume, database dump or application data is included.

## Preparation-host import

```bash
git clone https://github.com/Ever-Real/datariver-platform-amd64-distribution.git
cd datariver-platform-amd64-distribution
git lfs pull

sha256sum -c datariver-platform-amd64-3c0379f4051e.tar.gz.parts.sha256
cat datariver-platform-amd64-3c0379f4051e.tar.gz.part-aa \
  datariver-platform-amd64-3c0379f4051e.tar.gz.part-ab \
  > datariver-platform-amd64-3c0379f4051e.tar.gz
sha256sum -c datariver-platform-amd64-3c0379f4051e.tar.gz.sha256

mkdir -p restore
tar -xzf datariver-platform-amd64-3c0379f4051e.tar.gz -C restore
```

The extracted release directory is `restore/datariver-3c0379f4051e`. Load and verify the Core
bundle with its checked-in deployment scripts before starting DataRiver.

Load Redis separately:

```bash
sha256sum -c redis-8.2.6-bookworm-linux-amd64-3c0379f4051e.tar.gz.sha256
gzip -dc redis-8.2.6-bookworm-linux-amd64-3c0379f4051e.tar.gz | docker image load
docker image inspect --platform linux/amd64 redis:8.2.6-bookworm \
  --format '{{.Os}}/{{.Architecture}} {{.Id}}'
```

The image inspection must report `linux/amd64`.

Load Neo4j separately only when graph projection is enabled:

```bash
sha256sum -c neo4j-2026.06.0-linux-amd64.tar.gz.sha256
gzip -dc neo4j-2026.06.0-linux-amd64.tar.gz | docker image load
docker image inspect --platform linux/amd64 neo4j:2026.06.0 \
  --format '{{.Os}}/{{.Architecture}} {{.Id}}'
```

The image inspection must report `linux/amd64` and image ID
`sha256:5cf053cb7808bc822c0ca0529252577ecd964f2e67c3083413d51c15dfafc609`.

### Direct browser download: pgvector (no Git LFS client)

On the GitHub Files page, download
`pgvector-0.8.2-pg17-bookworm-linux-amd64.tar.gz` and its `.sha256` sidecar directly (use the
file's **Download** control for the Git LFS object). A Git LFS client is not required on the
preparation PC.

Verify and load the downloaded files:

```bash
sha256sum -c pgvector-0.8.2-pg17-bookworm-linux-amd64.tar.gz.sha256
gzip -dc pgvector-0.8.2-pg17-bookworm-linux-amd64.tar.gz | docker image load
docker image inspect --platform linux/amd64 \
  pgvector/pgvector:0.8.2-pg17-bookworm \
  --format '{{.Os}}/{{.Architecture}} {{.Id}}'
```

The image inspection must report `linux/amd64`. With that tag present, no
`SOURCE_HOST_POSTGRES_IMAGE` override is needed.

Install the source-host Python dependencies without reaching PyPI:

```bash
sha256sum -c datariver-uv-cache-linux-x86_64-a66012e1308b.tar.gz.sha256
test "$(sha256sum ../datariver_v1/uv.lock | awk '{print $1}')" = \
  "$(awk -F '\t' '$1 == "lock_sha256" {print $2}' \
    datariver-uv-cache-linux-x86_64-a66012e1308b.manifest.tsv)"

cache_parent=${XDG_CACHE_HOME:-"$HOME/.cache"}
mkdir -p "$cache_parent"
tar -xzf datariver-uv-cache-linux-x86_64-a66012e1308b.tar.gz -C "$cache_parent"

cd ../datariver_v1
UV_CACHE_DIR="$cache_parent/uv" uv sync --frozen --all-extras --offline
```

Use this archive only when `uname -m` reports `x86_64`, `python3.12 --version` reports Python
3.12.12 and `uv --version` reports 0.9.17. A changed `uv.lock` must use a newly generated and
verified cache archive.
