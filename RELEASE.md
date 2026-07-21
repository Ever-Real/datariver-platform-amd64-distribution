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
