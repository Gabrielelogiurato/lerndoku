---
title: Docker
sidebar_position: 2
---

# Docker Deployment

## Voraussetzungen

- Docker muss installiert sein (https://www.docker.com/products/docker-desktop).
- Ein Projekt haben mit z.B. TypeScript/Node.js

---

## Projektstruktur (Beispiel)

```
my-app/
├── src/
│   └── index.ts
├── package.json
├── tsconfig.json
├── Dockerfile
└── .dockerignore
```

---

## Dockerfile

```Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY .. . <!--Erster . heisst in diesem Ordner und Zweiter . im Ziel Ordner.-->
RUN npm run build
CMD ["node", "dist/index.js"]
EXPOSE 3000
```

---

## .dockerignore

```
node_modules
dist
*.log
Dockerfile
.dockerignore
```

---

## Build & Run

```bash
docker build -t my-app:latest .
docker run -d -p 3000:3000 --name my-app-container my-app:latest
docker logs -f my-app-container
```

---

## Containerverwaltung

```bash
docker ps -a
docker stop <container>
docker rm <container>
```

---

## Docker-Befehle

### Images

- `docker build -t my-app:latest .`
- `docker image ls`
- `docker rmi <image-id>`
- `docker load -i my-app.tar`

### Container

- `docker run -it ubuntu bash`
- `docker run -d -p 3000:3000 my-app`
- `docker exec -it my-app-container sh`
- `docker logs -f my-app-container`
- `docker rm <container-id>`

### Volumes

- `docker volume create my-volume`
- `docker volume ls`
- `docker volume rm my-volume`

### Cleanup

```bash
docker stop (name von Container)
docker rm (name von Container)
docker rmi (name von image)
docker volume prune -f
```

---

## Docker Compose

```yaml
version: "3.8"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    command: npm run dev
```

Starten mit:

```bash
docker compose up --build
```

---

## Multi-Stage Build (in der Schule gemacht)

```Dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY . .
RUN npm install && npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
```
