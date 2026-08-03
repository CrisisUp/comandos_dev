# 📘 Boas Práticas no Linux

> Guia de boas práticas para usar Linux no dia a dia de forma segura, eficiente e organizada

---

## 🛡️ Segurança Básica do Sistema

### 1. Use sudo com cuidado

```bash
# ❌ Evite trabalhar o dia todo como root
sudo su -

# ✅ Use sudo apenas no comando necessário
sudo apt update
sudo systemctl restart nginx

# ✅ Revise o que vai rodar antes de executar
cat script.sh   # leia antes de dar permissão/executar
```

### 2. Mude o usuário padrão do root

```bash
# Verificar se root tem senha
sudo passwd -S root

# Desabilitar login root via SSH (em produção)
# /etc/ssh/sshd_config:
# PermitRootLogin no
sudo systemctl restart ssh
```

## 🔑 Permissões e Donos

### 1. Menor privilégio

```bash
# ❌ NÃO usar 777
chmod 777 arquivo

# ✅ Mínimo necessário
chmod 640 arquivo   # dono rw, grupo r
chmod 755 pasta     # dono rwx, resto rx
chmod 600 segredo   # só dono
```

### 2. Donos corretos

```bash
# ✅ Aplicar dono/grupo coerente
sudo chown usuario:grupo arquivo
sudo chown -R usuario:grupo /var/www/

# Verificar donos
ls -l
```

## 🔍 Boas Práticas de Busca

```bash
# ✅ Sempre limitar busca para evitar lentidão
find . -name "*.log" -mtime -7        # não em todo o /
find . -size +100M

# Nome de arquivo com espaço: aspas
find . -name "meu arquivo.txt"

# Proteger contra quebra de linha em nomes com xargs -0
find . -name "*.txt" -print0 | xargs -0 grep "termo"
```

## 📦 Gerenciamento de Pacotes

- `Atualizar de forma regular`

```bash
# ✅ Atualizar índices primeiro, depois pacotes
sudo apt update
sudo apt upgrade

# ❌ Não usar apt upgrade sem revisar (especialmente em servidores)
sudo apt-get dist-upgrade   # revisar antes em produção
```

- `Instalar só o necessário`

```bash
# ✅ Menos pacotes = menor superfície de ataque
sudo apt install --no-install-recommends curl

# Ver o que vai instalar antes
apt show nomedopacote
apt-cache depends nomedopacote
```

## 🧹 Limpeza e Manutenção

```bash
# Remover pacotes órfãos e cache
sudo apt autoremove
sudo apt autoclean

# Ver espaço
df -h
du -sh /var/log/

# Limpar logs antigos
sudo journalctl --vacuum-time=7d
```

## 🖥️ Processos e Recursos

```bash
# ❌ matar processos sem entender
kill -9 PID

# ✅ primeiro checar, depois encerrar graciosamente
ps aux | grep processo
kill PID                 # SIGTERM (permite cleanup)
kill -9 PID              # último recurso (SIGKILL)

# Processos perigosos por usuário/pera
pkill -u usuario
```

## 🌐 Rede e Conectividade

### Boas práticas de firewall

```bash
# ✅ só abra portas necessárias
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw enable

# ❌ Não deixar serviços expostos sem proteção
```

### Comandos de inspeção

```bash
# Ver quem está escutando
ss -tulpn
netstat -tulpn

# Verificar conectividade
ping -c 4
nc -zv host porta
dig +short host
```

## 📁 Organização de Arquivos

- Convenções úteis
  - Use `~/` para arquivos pessoais, `/tmp/` para temporários.
  - Nomes em `kebab-case` ou `snake_case` (sem espaços).
  - Versionar arquivos de config importantes (`~/.zshrc`, `/etc/nginx/`).

## 🧪 Testes e Revisão

```bash
# Antes de rodar um script:
# 1. Leia o conteudo
less script.sh

# 2. Faça backup de outros arquivos
cp arquivo arquivo.bak

# 3. Execute com um exemplo pequeno
bash -n script.sh   # check sintaxe
bash -x script.sh   # rastrear execução
```

## 📋 Checklist de Boas Práticas Linux

* 🔐 Não usar root rotineiramente; `sudo` só quando necessário
* 🔑 Permissões mínimas (`644/755`, não `777`)
* 📦 Atualizar pacotes com regularidade
* 🧹 Limpar logs e pacotes órfãos
* 🐌 Encerrar processos com `SIGTERM` primeiro
* 🌐 Fechar portas desnecessárias no firewall
* 💾 Fazer backup antes de operações críticas
* ✅ Testar scripts em ambientes de staging

## 🎯 Resumo

| Prática | Recomendação |
|-------- |------------- |
| Acesso | `sudo` por comando, não root rotineiro |
| Permissões | Mínimo necessário (`644/755`) |
| Pacotes | Instalar o essencial, atualizar regularmente |
| Processos | Matar com sinais graduais |
| Rede | Portas apenas necessárias |
| Logs | Manter limpos e monitorados |

## 📚 Referências

* Manual `man`
* TLDR Pages
* Ubuntu Server Guide
* Red Hat System Administration Guide

Pronto para administrar Linux com boas práticas! 🐧🚀