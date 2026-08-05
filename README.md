# DataRiver Platform AMD64 Base Image Distribution

linux/amd64 플랫폼용 Docker 베이스 이미지 번들.  
준비PC(WSL/amd64) 오프라인 환경에서 `docker build` 및 `export_release.sh`에 사용합니다.

## 이미지 목록

| 파일 | 이미지 | 용도 |
|---|---|---|
| redis.tar | redis:8.2.6-bookworm@sha256:3055dc... | export_release.sh 번들 포함 |
| pgvector.tar | pgvector/pgvector:0.8.2-pg17-bookworm@sha256:feb68f... | postgres Dockerfile FROM |
| keycloak.tar | quay.io/keycloak/keycloak:26.7.0@sha256:2eb3cd... | keycloak Dockerfile FROM |
| uv.tar | ghcr.io/astral-sh/uv:0.9.17@sha256:5cb6b5... | backend Dockerfile FROM |
| python.tar | python:3.12.12-slim-bookworm@sha256:593bd0... | backend Dockerfile FROM |
| node.tar | node:22.19.0-bookworm-slim@sha256:4a4884... | frontend Dockerfile FROM (build) |
| nginx.tar | nginx:1.30.3-alpine3.23@sha256:0d3b80... | frontend Dockerfile FROM (runtime) |

## 준비PC 로드 방법

```bash
git clone https://github.com/Ever-Real/datariver-platform-amd64-distribution ~/aux-images
cd ~/aux-images
git lfs pull
sha256sum --check SHA256SUMS
for f in *.tar; do echo "Loading $f..."; docker image load < "$f"; done
```

## 업데이트 정책

datariver_v1 소스의 Dockerfile 또는 `export_release.sh`에서 sha256이 변경되는 커밋이
있을 때만 이 레포를 갱신합니다. 일상적인 개발 사이클에서는 재이관 불필요.
