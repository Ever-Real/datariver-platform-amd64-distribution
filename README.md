# DataRiver AMD64 offline platform release

This repository intentionally contains one Git commit and one current, closed-network
`linux/amd64` platform release. Large artifacts are stored in Git LFS.

## Release

`3c0379f4051e` — source commit `3c0379f4051ec698a53db41c5b5092895e40b8fb`

The Core archive contains the images required to run the DataRiver platform itself:

- PostgreSQL 17.10
- DataRiver backend image, including migrate, storage-init, bootstrap, API and workers
- DataRiver web image
- DataRiver Keycloak 26.7.0 image

Redis 8.2.6 is provided as a separate AMD64 image because the platform runs its cache and delivery
services through `compose.local-connectors.yaml`.

This release deliberately excludes external or optional services: MinIO, DataHub, Airflow, Neo4j,
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
