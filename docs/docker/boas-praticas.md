# 📘 Boas Práticas no Docker

> Guia de boas práticas para criar e gerenciar containers Docker de forma eficiente e segura

---

## 🐳 Dockerfile

### 1. Use imagens oficiais e específicas

```dockerfile
# ✅ Bom - Versão específica e oficial
FROM node:20-slim
FROM python:3.12-slim
FROM openjdk:17-slim

# ❌ Ruim - Versão genérica ou não oficial
FROM node:latest
FROM python
FROM ubuntu
```

### 2. Ordene os comandos para aproveitar cache

```dockerfile
# ✅ Bom - Dependências primeiro (mudam menos)
FROM node:20-slim
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm", "start"]

# ❌ Ruim - Código primeiro (invalida cache sempre)
FROM node:20-slim
COPY . .
RUN npm install
```

### 3. Use multi-stage builds

```dockerfile
# ✅ Bom - Build e runtime separados
FROM node:20-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

FROM node:20-slim AS runner
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
CMD ["node", "dist/index.js"]
```

### 4. Execute como usuário não-root

```dockerfile
# ✅ Bom - Usuário não-root para segurança
FROM node:20-slim
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001
USER nodejs
WORKDIR /app
COPY --chown=nodejs:nodejs . .
CMD ["node", "index.js"]

# ❌ Ruim - Executando como root
FROM node:20-slim
WORKDIR /app
COPY . .
CMD ["node", "index.js"]
```

### 5. Use .dockerignore

```dockerignore
# .dockerignore
node_modules/
npm-debug.log
.env
.git
.gitignore
README.md
Dockerfile
.dockerignore
*.log
*.tmp
coverage/
.nyc_output/
.vscode/
.idea/
```

### 6. Combine RUN commands

```dockerfile
# ✅ Bom - Menos camadas
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# ❌ Ruim - Muitas camadas desnecessárias
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get clean
```

## 📦 Imagens

### 1. Use imagens pequenas

```dockerfile
# ✅ Bom - Imagem pequena
FROM node:20-slim
FROM python:3.12-slim
FROM alpine:3.19

# ❌ Ruim - Imagem grande
FROM node:20
FROM ubuntu:22.04
FROM debian:bookworm
```

### 2. Remova pacotes desnecessários

```dockerfile
# ✅ Bom - Remove arquivos temporários
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ❌ Ruim - Deixa arquivos temporários
RUN apt-get update
RUN apt-get install -y curl
```

### 3. Versione suas imagens

```bash
# ✅ Bom - Tags específicas
docker build -t minha-app:1.0.0 .
docker tag minha-app:1.0.0 meu-registro/minha-app:1.0.0

# ❌ Ruim - Sem versão ou latest
docker build -t minha-app .
docker tag minha-app:latest meu-registro/minha-app:latest
```

## 🔒 Segurança

### 1. Não armazene secrets no Dockerfile

```dockerfile
# ❌ NUNCA FAÇA ISSO
ENV DB_PASSWORD=senha123
ENV API_KEY=abc123
RUN echo "secret" > /app/config.json

# ✅ Use secrets via build args ou arquivos externos
ARG DB_PASSWORD
ENV DB_PASSWORD=$DB_PASSWORD

# Ou use Docker secrets em produção
```

### 2. Use USER não-root

```dockerfile
# ✅ Bom
FROM node:20-slim
RUN adduser -D appuser
USER appuser
WORKDIR /app
COPY --chown=appuser:appuser . .
```

### 3. Escaneie vulnerabilidades

```bash
# Usar Trivy
trivy image minha-app:1.0.0

# Usar Grype
grype minha-app:1.0.0

# Usar Docker Scout (integrado)
docker scout quickview minha-app:1.0.0
```

### 4. Use read-only filesystem

```yaml
# docker-compose.yml
services:
  app:
    image: minha-app:1.0.0
    read_only: true
    tmpfs:
      - /tmp
```

## 🚀 Docker Compose

### 1. Use health checks

```yaml
# docker-compose.yml
services:
  db:
    image: postgres:16
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  app:
    image: minha-app:1.0.0
    depends_on:
      db:
        condition: service_healthy
```

### 2. Defina limites de recursos

```yaml
# docker-compose.yml
services:
  app:
    image: minha-app:1.0.0
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
# ⚠️ deploy.resources só é aplicado no modo Swarm
# (docker stack deploy); em compose local é ignorado.
```

### 3. Use variáveis de ambiente

```yaml
# docker-compose.yml
services:
  app:
    image: minha-app:1.0.0
    environment:
      - DB_HOST=${DB_HOST:-localhost}
      - DB_PORT=${DB_PORT:-5432}
    env_file:
      - .env
      - .env.production
```

### 4. Use volumes para dados persistentes

```yaml
# docker-compose.yml
services:
  db:
    image: postgres:16
    volumes:
      - db_data:/var/lib/postgresql/data
      - ./backups:/backups

volumes:
  db_data:
```

## 📊 Logs e Monitoramento

### 1. Log para stdout/stderr

```bash
# ✅ Bom
node index.js
python app.py
npm start

# ❌ Ruim - Log para arquivos internos
node index.js > /var/log/app.log
python app.py > /var/log/app.log
```

### 2. Use drivers de log

```yaml
# docker-compose.yml
services:
  app:
    image: minha-app:1.0.0
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### 3. Adicione labels para organização

```yaml
# docker-compose.yml
services:
  app:
    image: minha-app:1.0.0
    labels:
      - "com.example.environment=production"
      - "com.example.team=backend"
      - "com.example.version=1.0.0"
```

## 🔄 CI/CD

### 1. Use build cache

```yaml
# GitHub Actions
- name: Build Docker image
  uses: docker/build-push-action@v5
  with:
    context: .
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

### 2. Verifique tamanho da imagem

```yaml
# GitHub Actions
- name: Check image size
  run: |
    IMAGE_BYTES=$(docker image inspect minha-app --format "{{.Size}}")
    echo "Image size: $IMAGE_BYTES bytes"
    if [ "$IMAGE_BYTES" -gt 524288000 ]; then   # > 500MB (500 * 1024^2)
      echo "⚠️ Image is larger than 500MB!"
    fi
```

### 3. Execute testes antes do build

```yaml
# GitHub Actions
- name: Run tests
  run: |
    docker build -t test-image -f Dockerfile.test .
    docker run test-image npm test
```

## 🛠️ Comandos Úteis

* `Limpeza`

```bash
# Remover containers parados
docker container prune

# Remover imagens não usadas
docker image prune

# Remover tudo não usado
docker system prune -a

# Remover volumes não usados
docker volume prune
```

* `Inspeção`

```bash
# Ver logs
docker logs -f container_name

# Ver processos
docker top container_name

# Ver recursos
docker stats container_name

# Ver detalhes
docker inspect container_name
```

* `Debug`

```bash
# Entrar no container
docker exec -it container_name sh
docker exec -it container_name bash

# Copiar arquivos
docker cp arquivo.txt container_name:/app/

# Ver alterações
docker diff container_name
```

## 📋 Checklist

* `Dockerfile`
  - Usa imagem oficial e específica
  - Usa multi-stage builds (quando aplicável)
  - Ordena comandos para aproveitar cache
  - Usa usuário não-root
  - Tem .dockerignore
  - Não contém secrets
  - Combina RUN commands
  - Usa imagem pequena (slim/alpine)

* `Docker Compose`
  - Define health checks
  - Limita recursos
  - Usa variáveis de ambiente
  - Configura volumes persistentes
  - Configura logging adequado
  - Adiciona labels

* `Segurança`
  - Escaneia vulnerabilidades
  - Remove pacotes desnecessários
  - Usa read-only filesystem (quando possível)
  - Mantém imagens atualizadas

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Imagem base | Oficial, específica e slim |
| Usuário | Não-root sempre |
| Cache | Dependências primeiro |
| Layers | Combinar RUN commands |
| Segurança | Sem secrets, escaneie |
| Tamanho | Minimize imagem |
| Versão | Tags específicas |
| Logs | stdout/stderr |
| Recursos | Limitar CPU/Memória |

## 📚 Referências

* Dockerfile Best Practices
* Docker Security Best Practices
* Docker Compose Best Practices
* Docker Optimization