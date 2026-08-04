# 📘 Comandos Avançados do Docker

> Guia para usuários que já dominam o básico e querem controle fino sobre containers, redes, volumes e orquestração

---

## 🏗️ Dockerfile Avançado

* `Multi-stage builds`

```dockerfile
# Build e runtime em estágios separados
FROM golang:1.22 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /bin/app .

FROM alpine:3.19 AS runner
COPY --from=builder /bin/app /bin/app
EXPOSE 8080
ENTRYPOINT ["/bin/app"]
```

* `ARG vs ENV`

```dockerfile
# ARG = apenas em build time
ARG VERSION=1.0.0
RUN echo "Buildando ${VERSION}"

# ENV = disponível em build e runtime
ENV APP_ENV=production
```

* `Healthcheck`

```dockerfile
FROM node:20-slim
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1
```

* `Overrides de entrada`

```dockerfile
# Forma exec (recomendada - PID 1 correto)
ENTRYPOINT ["node", "server.js"]
CMD ["--port", "3000"]

# Suprimir sinais pode ser um problema - use dumb-init/tini
RUN apk add --no-cache tini
ENTRYPOINT ["/sbin/tini", "--", "node", "server.js"]
```

## 📦 Construção e Push Avançado

* `Build com cache e platormas`

```bash
# Build com build args e cache controlado
docker build --build-arg VERSION=2.0 --cache-from app:cache .

# Build para múltiplas arquiteturas (BuildKit)
DOCKER_BUILDKIT=1 docker buildx build --platform linux/amd64,linux/arm64 .

# Push com tags
docker buildx build --platform \
  linux/amd64,linux/arm64 -t usuario/app:latest -t usuario/app:1.0.0 --push .
```

* `Registries e autenticação`

```bash
# Login
docker login
docker login registry.example.com -u usuario -p senha

# Criar registry local
docker run -d -p 5000:5000 --name registry registry:2

# Enviar para registry local
docker tag app:1.0 localhost:5000/app
docker push localhost:5000/app

# Limpar credenciais
docker logout
```

## 🌐 Redes Avançadas

### Tipos de rede

```bash
# bridge - rede padrão de containers
docker network create --driver bridge --subnet=172.20.0.0/16 minha-bridge

# host - usa a rede do host diretamente
docker run --network host nginx

# none - sem conectividade
docker run --network none app
```

### Rede personalizada com containers

```bash
# Conectar múltiplas interfaces
docker network connect red-app app1
docker network connect red-db app1

# Inspecionar redes
docker network inspect red-app

# Rede com IPv6
docker network create --ipv6 --subnet=2001:db8::/64 minha-rede6
```

## 💾 Volumes e Storage Drivers

### Gerenciamento de storage

```bash
# Volumes nomeados com driver local (padrão)
docker volume create meu-volume --driver local

# Backup/restauração de volume
docker run --rm -v meu-volume:/data:ro -v $(pwd):/backup \
  alpine tar czf /backup/volume.tar.gz -C /data .

# Restaurar
docker run --rm -v meu-volume:/data -v $(pwd):/backup \
  alpine tar xzf /backup/volume.tar.gz -C /data
```

### Mount types

```bash
# Bind mount com opções
docker run -v /host:/container:ro,nosuid,noexec app

# Mount com tipo volume e source
docker run --mount type=volume,src=vol,dst=/data,volume-nocopy app

# Mount tmpfs (memória, não persiste)
docker run --mount type=tmpfs,dst=/app/tmpfs app
```

## 🔍 Inspeção e Diagnóstico

### Docker stats e logs avançados

```bash
# Monitoramento
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"

# Logs com filtro
docker logs --since 10m -f app > /tmp/app.log
docker logs --tail 50 --timestamps app
docker logs --details app

# Inspeção detalhada
docker inspect --format '{{.Config.Image}}'
docker inspect --format '{{json .State}}' app
docker inspect --format '{{range .Mounts}}{{.Source}}{{end}}' app
```

### Debug avançado

```bash
# Entrar no namespace do container (sem docker exec)
nsenter -n -t $(docker inspect -f '{{.State.Pid}}' app)
docker run --rm --pid=host --privileged \
  alpine sh -c 'cat /proc/<pid>/environ'

# Ver diffs de arquivos
docker diff app
```

## 🔄 Docker Compose Avançado

### docker-compose.yml com vários serviços

```yaml
services:
  app:
    build: .
    depends_on:
      db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 5s
      timeout: 3s
      retries: 10
    networks:
      - front
      - back
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=db
    volumes:
      - ./uploads:/uploads

  db:
    image: postgres:16
    restart: always
    environment:
      POSTGRES_PASSWORD: ${DB_PASS}
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - back

volumes:
  db_data:

networks:
  front:
    driver: bridge
  back:
    internal: true
```

### Comandos Compose avançados

```bash
# Subir um serviço específico
docker compose up -d db

# Logs de um serviço
docker compose logs -f app --tail=50

# Executar comando em serviço
docker compose exec app sh

# Aplicar mudanças sem recriar tudo
docker compose up -d --no-deps app

# Ver dependências entre serviços
docker compose config --services
docker compose config --volumes
docker compose config --images
```

## 🔐 Segurança Avançada

* `Run sem privilégios e com capability mínima`

```bash
# Sem capabilities desnecessárias
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE nginx

# Sem modo privilegiado
docker run --read-only --tmpfs /tmp nginx

# User e group
docker run -u 1000:1000 nginx

# Escanear imagens
docker scout cves app:1.0
docker scan app:1.0
trivy image app:1.0
```

* `Secrets e configs (Swarm)`

```bash
# Secrets via Docker Secrets in Swarm
echo "supersecure123" | docker secret create app_db_pass -
docker secret ls
docker secret inspect app_db_pass
docker secret rm app_db_pass

# Volume de secrets em runtime
docker run -v /run/secrets/app_db_pass:/etc/app_db_pass app
```

* `Squash & flatten`

```bash
# Squash todas as camadas em uma (docker build --squash é experimental)
docker export container | docker import - my:flat
docker history minha-app
docker inspect --format '{{len .RootFS.Layers}}' minha-app
```

## 🔍 Limpeza de Disco Avançada

```bash
# Ver quanto cada imagem pesa
docker system df
docker system df -v

# Remover tudo [+ excluir imagens utilizadas]
docker system prune -a --volumes

# Filter regras
docker system prune --filter "until=24h"
docker volume prune --filter "label=app=web"
docker image prune --filter "dangling=true"
```

## 📝 Aliases e utilitários

```bash
# Aliases Dockerfile
alias dbuild="docker build -t"
alias drun="docker run -it --rm"
alias dexec="docker exec -it"
alias dpf="docker ps --filter"
alias dstats="docker stats --no-stream"
```

## 🆘 Solução de dúvidas

```bash
# Ver eventos do daemon
docker events --filter 'type=image' --since 1m

# Ver informações do daemon
docker info --format '{{.ServerVersion}}'
docker info --format '{{.OSType}}'

# Ajuda
docker <comando> --help
docker inspect ID --format '{{json .}}'
```

## 📋 Checklist de Docker Avançado

| Tarefa | Comando |
| ------ | ------- |
| Build multi-arch | `docker buildx build --platform` |
| Netear volumes | `docker volume prune` |
| Inspecionar | `docker inspect <id>` |
| Compose up de serviço | `docker compose up -d db` |
| Scan de imagem | `docker scout cves app` |
| Criar rede | `docker network create` |
| Logs filtrados | `docker logs --since 1h app` |
| Eliminar tudo | `docker system prune --all --volumes` |

## 📚 Referências

* Docker CLI Reference
* Dockerfile Best Practices
* Docker Compose Reference
* Exemplos oficiais na Docker Hub

Pronto para operar Docker em produção! 🐳🚀