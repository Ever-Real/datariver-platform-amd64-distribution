# Release record

- Distribution platform: `linux/amd64`
- Source repository: `https://github.com/JayJin/datariver_v1.git`
- Source revision: `f8baffc8cfeaf147b3ef1e01afea6b5c18ab0712`
- Source commit timestamp: `2026-07-21T16:10:56+09:00`
- Build topology: remote DataHub; DataHub images are intentionally excluded.

## Artifacts

| Artifact | Contents | Original tar SHA-256 |
| --- | --- | --- |
| `datariver-platform-amd64.tar.zst.part-000` through `003` | Core platform, PostgreSQL, Valkey, SeaweedFS, Keycloak, Airflow, APISIX and Neo4j | `cdd3a50f024d7de0fde9894fa3cdf0ab3a1ddb33a55ac129c3205ba7ff49e4e7` |
| `datariver-observability-pilot-amd64.tar.zst.part-000` through `001` | Optional pilot observability profile | `51213b9e30b2799ff9b8699b88757acaf2ada74fa8fc33cb0f32483710a228ca` |

Each split file is 500 MB except the final file of a bundle. The associated
`*.parts.sha256` file verifies every part. The original tar and image manifest
checksums are retained alongside the parts. Every image entry in both manifest
files is `linux/amd64`.

## Additional macOS arm64 source-host Python cache

- Artifact: `datariver-uv-cache-darwin-arm64-a66012e1308b.tar.gz`
- Artifact SHA-256: `827d68bd36e5c3679a49e2e63e5a8ea261e5184bad92e5ca91eb12af75a4331f`
- Source repository: `https://github.com/JayJin/datariver_v1.git`
- Source revision: `313e59ae42baa247fc9806df623227e8ff8f2917`
- Lockfile SHA-256: `a66012e1308be317e49c1768aaabc0d99c3b5a33e741cba2e5d7999d12e87cad`
- Target: macOS arm64, Python 3.12 and uv 0.9.17 only

The artifact contains the uv package cache required by the matching frozen
source-host dependency installation, including `pypdf==6.13.3` and
`python-multipart==0.0.31`. Its sidecar `.sha256` and `.manifest.tsv` files are
part of the release record. It is independent from the Linux AMD64 Docker image
bundles above.
