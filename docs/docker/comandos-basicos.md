# 📘 Comandos Básicos do Docker

> Guia essencial para começar a usar Docker no dia a dia

---

## 🐳 Gerenciamento de Containers

### Executar containers

```bash
# Executar um container e manter em primeiro plano
docker run nginx

# Executar em background (detached mode)
docker run -d nginx

# Executar com nome personalizado
docker run -d --name meu-nginx nginx

# Executar com porta mapeada
docker run -d -p 8080:80 nginx

# Executar e remover automaticamente ao sair
docker run --rm nginx

# Executar com variável de ambiente
docker run -e MINHA_VARIAVEL=valor nginx

# Executar com volume montado
docker run -v /host/pasta:/container/pasta nginx

# Executar com diretório de trabalho definido
docker run -w /app nginx

# Executar em modo interativo (com shell)
docker run -it ubuntu bash
docker run -it python:3.12 /bin/bash
Gerenciar containers em execução
bash
# Listar containers em execução
docker ps

# Listar todos os containers (incluindo parados)
docker ps -a

# Listar apenas IDs dos containers
docker ps -q

# Listar com formato personalizado
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Parar um container
docker stop container_name_or_id

# Parar todos os containers
docker stop $(docker ps -q)

# Iniciar um container parado
docker start container_name_or_id

# Reiniciar um container
docker restart container_name_or_id

# Matar um container (forçar parada)
docker kill container_name_or_id

# Pausar um container
docker pause container_name_or_id

# Despausar um container
docker unpause container_name_or_id

# Aguardar container terminar
docker wait container_name_or_id
Remover containers
bash
# Remover um container parado
docker rm container_name_or_id

# Remover container forçadamente
docker rm -f container_name_or_id

# Remover todos os containers parados
docker container prune

# Remover todos os containers (parados e em execução)
docker rm -f $(docker ps -aq)
📦 Gerenciamento de Imagens
Listar imagens
bash
# Listar imagens locais
docker images

# Listar com formato personalizado
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# Listar apenas IDs das imagens
docker images -q

# Listar imagens intermediárias
docker images -a
Baixar imagens
bash
# Baixar imagem do Docker Hub
docker pull nginx
docker pull nginx:latest
docker pull nginx:1.25

# Baixar imagem de repositório privado
docker pull meu-usuario/minha-imagem:1.0

# Baixar imagem do Docker Hub (outro registry)
docker pull registry.digitalocean.com/meu-registro/imagem:tag

# Baixar imagem sem verificar assinatura
docker pull --disable-content-trust nginx
Construir imagens
bash
# Construir imagem a partir do Dockerfile na pasta atual
docker build -t minha-imagem .

# Construir com nome e tag específicos
docker build -t minha-imagem:1.0.0 .

# Construir com Dockerfile em local diferente
docker build -t minha-imagem -f /caminho/Dockerfile .

# Construir sem usar cache
docker build --no-cache -t minha-imagem .

# Construir com build args
docker build --build-arg VARIAVEL=valor -t minha-imagem .

# Construir com target específico (multi-stage)
docker build --target builder -t minha-imagem .
Remover imagens
bash
# Remover imagem específica
docker rmi imagem_id_ou_nome

# Remover imagem forçadamente
docker rmi -f imagem_id_ou_nome

# Remover todas as imagens não usadas
docker image prune

# Remover todas as imagens não usadas (forçado)
docker image prune -a -f

# Remover imagens com padrão
docker rmi $(docker images | grep "padrao" | awk '{print $3}')
Tag e push
bash
# Criar tag para imagem
docker tag minha-imagem:1.0.0 meu-usuario/minha-imagem:1.0.0

# Tag para Docker Hub
docker tag minha-imagem:1.0.0 usuario/repositorio:tag

# Tag para registry privado
docker tag minha-imagem:1.0.0 registry.meu-dominio.com/minha-imagem:tag

# Enviar imagem para registry
docker push meu-usuario/minha-imagem:1.0.0

# Enviar todas as tags
docker push --all-tags meu-usuario/minha-imagem
🔍 Inspeção e Debug
Logs
bash
# Ver logs de um container
docker logs container_name_or_id

# Ver logs em tempo real
docker logs -f container_name_or_id

# Ver últimas N linhas
docker logs -n 50 container_name_or_id

# Ver logs com timestamp
docker logs -t container_name_or_id

# Ver logs desde um horário específico
docker logs --since 2024-01-01T10:00:00 container_name_or_id

# Ver logs de um intervalo
docker logs --since 10m container_name_or_id
Executar comandos
bash
# Executar comando no container em execução
docker exec container_name_or_id comando

# Executar em modo interativo
docker exec -it container_name_or_id /bin/bash

# Executar com diretório de trabalho
docker exec -w /app container_name_or_id comando

# Executar com variável de ambiente
docker exec -e VARIAVEL=valor container_name_or_id comando

# Executar como usuário específico
docker exec -u usuario container_name_or_id comando
```

* `Inspecionar`

```bash
# Ver detalhes do container
docker inspect container_name_or_id

# Ver IP do container
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id

# Ver variáveis de ambiente
docker inspect -f '{{range .Config.Env}}{{println .}}{{end}}' container_name_or_id

# Ver portas mapeadas
docker port container_name_or_id

# Ver processos no container
docker top container_name_or_id

# Ver estatísticas de uso
docker stats container_name_or_id
docker stats --no-stream

# Ver alterações no filesystem
docker diff container_name_or_id
```

* `Copiar arquivos`

```bash
# Copiar do host para o container
docker cp /host/arquivo.txt container_name_or_id:/container/caminho/

# Copiar do container para o host
docker cp container_name_or_id:/container/arquivo.txt /host/caminho/

# Copiar pasta recursivamente
docker cp /host/pasta container_name_or_id:/container/pasta/
```

### 🌐 Redes

* `Gerenciar redes`

```bash
# Listar redes
docker network ls

# Inspecionar rede
docker network inspect network_name

# Criar rede bridge
docker network create minha-rede

# Criar rede com driver específico
docker network create --driver bridge minha-rede

# Criar rede com subnet
docker network create --subnet=172.20.0.0/16 minha-rede

# Remover rede
docker network rm minha-rede

# Remover redes não usadas
docker network prune
```

* `Conectar containers`

```bash
# Conectar container a uma rede
docker network connect minha-rede container_name

# Desconectar container da rede
docker network disconnect minha-rede container_name

# Rodar container em uma rede específica
docker run -d --network minha-rede nginx
```

### 💾 Volumes

* `Gerenciar volumes`

```bash
# Listar volumes
docker volume ls

# Criar volume
docker volume create meu-volume

# Inspecionar volume
docker volume inspect meu-volume

# Remover volume
docker volume rm meu-volume

# Remover volumes não usados
docker volume prune
```

* `Usar volumes`

```bash
# Montar volume em container
docker run -v meu-volume:/app nginx

# Montar volume anônimo
docker run -v /app nginx

# Montar bind mount
docker run -v /host/pasta:/container/pasta nginx

# Montar volume com opções
docker run -v meu-volume:/app:ro nginx  # read-only

# Usar mount (mais verboso)
docker run --mount type=volume,source=meu-volume,target=/app nginx
docker run --mount type=bind,source=/host/pasta,target=/app nginx
```

### 🧹 Limpeza

* `Comandos de limpeza`

```bash
# Remover containers parados
docker container prune

# Remover imagens não usadas
docker image prune

# Remover redes não usadas
docker network prune

# Remover volumes não usados
docker volume prune

# Remover tudo não usado (cuidado!)
docker system prune

# Remover tudo não usado (incluindo imagens não taggeadas)
docker system prune -a

# Remover tudo forçado (sem confirmação)
docker system prune -a -f

# Ver uso de espaço
docker system df
```

### 📝 Aliases Úteis

* `Criar aliases no ~/.zshrc`

```bash
# Aliases para Docker
alias dps="docker ps"
alias dpsa="docker ps -a"
alias di="docker images"
alias drm="docker rm"
alias drmi="docker rmi"
alias dstop="docker stop"
alias dstart="docker start"
alias drestart="docker restart"
alias dlogs="docker logs -f"
alias dexec="docker exec -it"
alias dinspect="docker inspect"
alias dclean="docker system prune -a -f"

# Aliases para Docker Compose
alias dc="docker-compose"
alias dcu="docker-compose up"
alias dcd="docker-compose down"
alias dcb="docker-compose build"
alias dcl="docker-compose logs -f"
alias dcexec="docker-compose exec"
```

🆘 Ajuda

```bash
# Ajuda geral
docker --help

# Ajuda de um comando específico
docker run --help
docker build --help
docker ps --help
```

### 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `docker ps` | Ver containers em execução |
| `docker logs -f nome` | Ver logs em tempo real |
| `docker exec -it nome bash` | Entrar no container |
| `docker stop nome` | Parar container |
| `docker rm nome` | Remover container |
| `docker images` | Ver imagens locais |
| `docker system df` | Ver uso de espaço |

### 🎯 Resumo dos Comandos

| Categoria | Comando | Descrição |
| --------- | ------- | --------- |
| **Container** | `docker run` | Executar container |
| | `docker ps` | Listar containers |
| | `docker stop` | Parar container |
| | `docker start` | Iniciar container |
| | `docker rm` | Remover container |
| **Imagem** | `docker pull` | Baixar imagem |
| | `docker build` | Construir imagem |
| | `docker push` | Enviar imagem |
| | `docker images` | Listar imagens |
| | `docker rmi` | Remover imagem |
| **Inspeção** | `docker logs` | Ver logs |
| | `docker exec` | Executar comando |
| | `docker inspect` | Inspecionar |
| | `docker stats` | Ver recursos |
| **Rede** | `docker network` | Gerenciar redes |
| **Volume** | `docker volume` | Gerenciar volumes |
| **Limpeza** | `docker system prune` | Limpar tudo |

### 📚 Referências

* Documentação Oficial do Docker
* Docker CLI Reference
* Dockerfile Reference

---

## 📂 **Como salvar o arquivo**

```bash
# Criar a pasta docker se não existir
mkdir -p ~/Desktop/comandos_dev/docs/docker

# Salvar o arquivo
nano ~/Desktop/comandos_dev/docs/docker/comandos-basicos.md

# Colar o conteúdo acima
# Salvar: Ctrl+O, Enter
# Sair: Ctrl+X
```

### Pronto para usar Docker com confiança! 🐳🚀
