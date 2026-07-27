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
