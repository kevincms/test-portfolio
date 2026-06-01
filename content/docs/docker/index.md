---
title: Docker 기초 정리
weight: 10
date: 2026-05-31
summary: 자주 쓰는 Docker 명령어와 Dockerfile·Compose 패턴 정리.
categories:
  - DevOps
tags:
  - Docker
---

이미지를 빌드하고 컨테이너를 띄울 때 반복적으로 찾게 되는 내용을 정리한다. 명령어 레퍼런스보다 **"왜 / 언제 쓰는지"** 위주.

## 핵심 개념

| 용어 | 한 줄 정의 |
|---|---|
| **이미지(Image)** | 실행 환경을 통째로 굳힌 읽기 전용 스냅샷 |
| **컨테이너(Container)** | 이미지를 실행한 격리된 프로세스 인스턴스 |
| **레이어(Layer)** | Dockerfile 한 줄 = 한 레이어, 캐시 단위 |
| **볼륨(Volume)** | 컨테이너 밖에서 영속되는 데이터 저장소 |

## 자주 쓰는 명령어

```bash
# 이미지 빌드 (현재 디렉터리 Dockerfile)
docker build -t myapp:latest .

# 컨테이너 실행 (백그라운드 + 포트 매핑 + 이름 지정)
docker run -d -p 8080:80 --name web myapp:latest

# 실행 중인 컨테이너 목록 / 전체 목록
docker ps
docker ps -a

# 로그 따라 보기
docker logs -f web

# 컨테이너 안으로 들어가기
docker exec -it web /bin/sh

# 정리: 멈춘 컨테이너·미사용 이미지·네트워크 일괄 제거
docker system prune -af
```

## Dockerfile 패턴

레이어 캐시를 살리려면 **자주 바뀌는 것일수록 아래**에 둔다. 의존성 설치를 소스 복사보다 먼저 하는 게 핵심.

```dockerfile
FROM node:20-alpine AS base
WORKDIR /app

# 1) 의존성 먼저 — package.json이 안 바뀌면 이 레이어는 캐시됨
COPY package*.json ./
RUN npm ci --omit=dev

# 2) 소스는 마지막에 — 코드만 바뀌면 위 레이어 재사용
COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```

> **멀티스테이지 빌드**: 빌드 도구(컴파일러 등)는 `AS build` 스테이지에 두고, 최종 이미지에는 결과물만 `COPY --from=build` 해서 이미지 크기를 줄인다.

## docker compose

여러 컨테이너를 한 파일로 묶어 `docker compose up` 한 번에 띄운다.

```yaml
services:
  web:
    build: .
    ports:
      - "8080:80"
    depends_on:
      - db
    environment:
      DATABASE_URL: postgres://app:secret@db:5432/app
  db:
    image: postgres:16-alpine
    volumes:
      - pgdata:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: app

volumes:
  pgdata:
```

```bash
docker compose up -d      # 백그라운드 기동
docker compose logs -f    # 전체 로그
docker compose down       # 중지 + 네트워크 제거 (-v 붙이면 볼륨까지)
```

## 트러블슈팅

| 증상 | 원인 / 해결 |
|---|---|
| 빌드가 매번 느림 | `COPY . .`를 의존성 설치보다 먼저 둬서 캐시 무효화 → 순서 교정 |
| 컨테이너가 바로 종료됨 | 포그라운드 프로세스가 없음 → `CMD`가 데몬을 `-g 'daemon off;'` 등으로 포그라운드 실행하는지 확인 |
| 이미지가 너무 큼 | `alpine`/`slim` 베이스 + 멀티스테이지 + `.dockerignore`로 불필요 파일 제외 |
| 권한 오류 | 컨테이너 내부 UID 불일치 → `USER` 지정 또는 볼륨 권한 확인 |

## 참고

- 공식 문서: <https://docs.docker.com/>
- 이미지 검색: <https://hub.docker.com/>
