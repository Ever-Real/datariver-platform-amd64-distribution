# DataRiver Platform — Linux AMD64 offline distribution

This repository contains the DataRiver offline Docker image distribution for
`linux/amd64` (x86_64) Docker servers. It replaces the former ARM64 package.

It includes two independently verified bundles:

- `datariver-platform-amd64.tar.zst.part-*` — DataRiver core, PostgreSQL 17.10,
  Valkey, SeaweedFS, Keycloak, Airflow, APISIX and Neo4j.
- `datariver-observability-pilot-amd64.tar.zst.part-*` — optional Grafana,
  Prometheus, OTel Collector, Tempo, Loki and Alertmanager pilot profile.

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
