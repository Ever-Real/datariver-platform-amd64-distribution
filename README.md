# DataRiver Platform — Linux AMD64 offline distribution

This repository contains the DataRiver offline Docker image distribution for
`linux/amd64` (x86_64) Docker servers. It replaces the former ARM64 package.

It includes two independently verified bundles:

- `datariver-platform-amd64.tar.zst.part-*` — DataRiver core, PostgreSQL 17.10,
  Valkey, SeaweedFS, Keycloak, Airflow, APISIX and Neo4j.
- `datariver-observability-pilot-amd64.tar.zst.part-*` — optional Grafana,
  Prometheus, OTel Collector, Tempo, Loki and Alertmanager pilot profile.

It also includes a separate macOS arm64 host-source Python cache artifact. It is
not a Docker image and does not alter the Linux AMD64 image bundles.

The package deliberately does **not** include DataHub, source-system databases,
LLM models, secrets, `.env`, volumes or uploads. In the supported remote-DataHub
topology, DataHub remains an independently operated service.

## Download and load on the AMD64 target

```bash
git lfs install
git clone https://github.com/JayJin/datariver-platform-arm64-distribution.git
cd datariver-platform-arm64-distribution
git lfs pull

sha256sum -c datariver-platform-amd64.tar.zst.parts.sha256
cat datariver-platform-amd64.tar.zst.part-* > datariver-platform-amd64.tar.zst
zstd -t datariver-platform-amd64.tar.zst
zstd -d --stdout datariver-platform-amd64.tar.zst > datariver-platform-amd64.tar
sha256sum -c datariver-platform-amd64.tar.sha256
sha256sum -c datariver-platform-amd64.manifest.tsv.sha256
docker load -i datariver-platform-amd64.tar
```

Load the observability bundle only when that Compose profile is required:

```bash
sha256sum -c datariver-observability-pilot-amd64.tar.zst.parts.sha256
cat datariver-observability-pilot-amd64.tar.zst.part-* > datariver-observability-pilot-amd64.tar.zst
zstd -t datariver-observability-pilot-amd64.tar.zst
zstd -d --stdout datariver-observability-pilot-amd64.tar.zst > datariver-observability-pilot-amd64.tar
sha256sum -c datariver-observability-pilot-amd64.tar.sha256
sha256sum -c datariver-observability-pilot-amd64.manifest.tsv.sha256
docker load -i datariver-observability-pilot-amd64.tar
```

After decompression, verify the original tar checksum and manifest checksum using
the included `*.sha256` files before starting Compose. `RELEASE.md` records the
source revision and complete image inventory.

## Download the macOS arm64 Python cache

`datariver-uv-cache-darwin-arm64-a66012e1308b.tar.gz` contains the exact uv
cache for DataRiver source commit `313e59ae42baa247fc9806df623227e8ff8f2917`,
including `pypdf==6.13.3` and `python-multipart==0.0.31`. It is valid only for
macOS arm64, Python 3.12 and uv 0.9.17. It contains no secrets, `.env`, source
virtual environment, Docker image, volume or upload data.

To avoid downloading the unrelated LFS image bundles, retrieve only this cache
artifact on the preparation PC:

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/JayJin/datariver-platform-arm64-distribution.git
cd datariver-platform-arm64-distribution
git lfs pull --include='datariver-uv-cache-darwin-arm64-a66012e1308b.tar.gz'

shasum -a 256 -c datariver-uv-cache-darwin-arm64-a66012e1308b.tar.gz.sha256
cache_dir="$(uv cache dir)"
mkdir -p "$(dirname "$cache_dir")"
tar -xzf datariver-uv-cache-darwin-arm64-a66012e1308b.tar.gz -C "$(dirname "$cache_dir")"
```

In the DataRiver source checkout at the matching commit, install with the
unpacked cache only:

```bash
UV_CACHE_DIR="$cache_dir" uv sync --frozen --all-extras --offline
```
