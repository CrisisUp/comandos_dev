# 📘 Comandos Básicos do Linux

> Guia essencial para começar a usar Linux no dia a dia

---

## 📂 Navegação e Manipulação de Arquivos

### Navegação

```bash
# Ver diretório atual
pwd

# Listar arquivos
ls
ls -l          # Lista detalhada
ls -la         # Lista detalhada (incluindo ocultos)
ls -lh         # Lista com tamanhos legíveis
ls -lt         # Ordena por data
ls -ltr        # Ordena por data (invertido)
ls -R          # Lista recursivamente
ls -S          # Ordena por tamanho

# Mudar de diretório
cd /home/usuario
cd ~           # Ir para home
cd ..          # Voltar um diretório
cd -           # Voltar ao diretório anterior
cd /           # Ir para raiz

# Criar diretórios
mkdir pasta
mkdir -p pasta/subpasta/subsubpasta   # Cria diretórios aninhados

# Remover diretórios
rmdir pasta            # Remove diretório vazio
rm -rf pasta           # Remove diretório e conteúdo (CUIDADO!)
```

## 📝 Manipulação de Arquivos

```bash

# Criar arquivo
touch arquivo.txt
echo "Conteúdo" > arquivo.txt     # Cria arquivo com conteúdo
cat > arquivo.txt                 # Cria e permite digitar (Ctrl+D para sair)

# Visualizar arquivos
cat arquivo.txt          # Mostra todo o conteúdo
less arquivo.txt         # Visualiza paginado (espaço para avançar, q para sair)
head arquivo.txt         # Mostra as 10 primeiras linhas
head -n 20 arquivo.txt   # Mostra as 20 primeiras linhas
tail arquivo.txt         # Mostra as 10 últimas linhas
tail -n 20 arquivo.txt   # Mostra as 20 últimas linhas
tail -f arquivo.txt      # Acompanha arquivo em tempo real (logs)

# Copiar arquivos
cp arquivo.txt backup.txt
cp -r pasta/ backup/     # Copia diretório recursivamente
cp -i arquivo.txt destino/  # Pergunta antes de sobrescrever

# Mover/Renomear
mv arquivo.txt pasta/    # Move para pasta
mv nome_antigo.txt nome_novo.txt  # Renomeia

# Remover arquivos
rm arquivo.txt
rm -f arquivo.txt        # Força remoção (sem confirmação)
rm -i arquivo.txt        # Pergunta antes de remover
rm -rf pasta/            # Remove diretório e conteúdo (CUIDADO!)

# Links
ln -s /caminho/origem link_simbolico   # Cria link simbólico
ln /caminho/origem link_fisico          # Cria link físico
```

## 📝 Editor de Texto (Nano)

```bash

# Abrir/Criar arquivo
nano arquivo.txt

# Atalhos do Nano
Ctrl+O          # Salvar (Write Out)
Ctrl+X          # Sair (Exit)
Ctrl+W          # Buscar (Where is)
Ctrl+\          # Substituir
Ctrl+C          # Ver posição do cursor
Ctrl+K          # Recortar linha
Ctrl+U          # Colar
Alt+U           # Desfazer
Alt+E           # Refazer
Ctrl+_          # Ir para linha específica
```

## 👥 Gerenciamento de Usuários

```bash

# Ver usuário atual
whoami
id

# Trocar de usuário
su - usuario
sudo comando

# Ver usuários logados
who
w                 # Mais detalhado
last              # Histórico de logins

# Adicionar usuário
sudo adduser nome_usuario
sudo useradd -m nome_usuario

# Remover usuário
sudo deluser nome_usuario
sudo userdel -r nome_usuario

# Alterar senha
passwd
sudo passwd nome_usuario

# Ver grupos
groups
groups nome_usuario

# Adicionar usuário a grupo
sudo usermod -aG grupo nome_usuario
```

## 📊 Permissões

* `Alterar permissões`

```bash

# Ver permissões
ls -l arquivo.txt
# Exemplo: -rw-r--r-- 1 user group 1234 Jan 01 12:00 arquivo.txt

# Alterar permissões (modo numérico)
chmod 755 arquivo.txt
# 7 = rwx (4+2+1), 5 = r-x (4+0+1)
# 755 = usuário tem rwx, grupo tem r-x, outros tem r-x

# Alterar permissões (modo simbólico)
chmod u+x arquivo.txt      # Adiciona execução para usuário
chmod g-w arquivo.txt      # Remove escrita do grupo
chmod o+r arquivo.txt      # Adiciona leitura para outros
chmod a+x arquivo.txt      # Adiciona execução para todos

# Mudar proprietário
sudo chown usuario:grupo arquivo.txt
sudo chown usuario arquivo.txt
sudo chgrp grupo arquivo.txt
```

## 🔢 Permissões numéricas

```bash

# 4 = Leitura (r)
# 2 = Escrita (w)
# 1 = Execução (x)

# 7 = rwx (4+2+1)  # Leitura, escrita, execução
# 6 = rw- (4+2)    # Leitura, escrita
# 5 = r-x (4+1)    # Leitura, execução
# 4 = r-- (4)      # Leitura apenas
# 0 = --- (0)      # Sem permissões

# Exemplos
chmod 700 script.sh    # Apenas você pode tudo
chmod 755 script.sh    # Você pode tudo, outros leem/executam
chmod 644 arquivo.txt  # Você pode ler/escrever, outros leem
chmod 600 arquivo.txt  # Apenas você pode ler/escrever
chmod 400 arquivo.txt  # Apenas você pode ler
```

## 🔍 Busca

```bash

# Buscar arquivos
find / -name "arquivo.txt"          # Busca pelo nome
find / -name "*.txt"                 # Busca por extensão
find / -type f -name "*.log"         # Busca arquivos .log
find / -type d -name "pasta"         # Busca diretórios
find . -size +100M                   # Busca arquivos maiores que 100MB
find . -mtime -7                     # Modificados nos últimos 7 dias
find . -mtime +30                    # Modificados há mais de 30 dias
find . -empty                        # Arquivos/diretórios vazios

# Buscar em arquivos (grep)
grep "texto" arquivo.txt
grep -i "texto" arquivo.txt        # Case insensitive
grep -r "texto" ./                 # Busca recursiva
grep -v "texto" arquivo.txt        # Exclui linhas com "texto"
grep -n "texto" arquivo.txt        # Mostra números de linha
grep -c "texto" arquivo.txt        # Conta linhas com ocorrência (não ocorrências)
# para contar ocorrências: grep -o "texto" arquivo.txt | wc -l
grep -E "regex" arquivo.txt        # Usa expressão regular

# Buscar comandos
which comando         # Localiza comando
whereis comando       # Localiza comando, fonte e manpage
type comando          # Mostra o tipo do comando
```

## 📦 Gerenciamento de Pacotes

* `APT (Debian/Ubuntu)`

```bash
# Atualizar lista de pacotes
sudo apt update

# Atualizar pacotes
sudo apt upgrade

# Atualizar tudo (incluindo dependências)
sudo apt full-upgrade

# Instalar pacote
sudo apt install nome_pacote

# Remover pacote
sudo apt remove nome_pacote
sudo apt purge nome_pacote       # Remove com configurações

# Buscar pacote
apt search termo
apt show nome_pacote

# Listar pacotes instalados
apt list --installed
dpkg -l

# Limpar pacotes não usados
sudo apt autoremove
sudo apt autoclean
```

* `DNF/YUM (Fedora/RHEL)`

```bash
# Atualizar
sudo dnf update
sudo dnf upgrade

# Instalar
sudo dnf install nome_pacote

# Remover
sudo dnf remove nome_pacote

# Buscar
dnf search termo
dnf info nome_pacote
```

* `YUM (RHEL/CentOS 7)`

```bash

# Atualizar
sudo yum update
sudo yum upgrade

# Instalar
sudo yum install nome_pacote

# Remover
sudo yum remove nome_pacote

# Buscar
yum search termo
yum info nome_pacote
```

## 🎯 Processos

```bash
# Ver processos
ps
ps aux | grep comando   # Ver todos os processos
ps -ef | grep comando

# Ver processos em tempo real
htop
top
```

### Top - Comandos interativos

```bash
top  # Inicia o top
# Atalhos:
# h - Ajuda
# q - Sair
# P - Ordenar por CPU
# M - Ordenar por Memória
# T - Ordenar por Tempo
# k - Matar processo
# u - Filtrar por usuário
# 1 - Mostrar todos os CPUs
```

* `Gerenciar processos`

```bash
# Matar processos
kill PID
kill -9 PID           # Mata forçadamente
killall nome_processo
pkill nome_processo

# Prioridade
nice -n 10 comando    # Inicia com prioridade mais baixa
renice 10 -p PID      # Altera prioridade de processo em execução

# Background/Foreground
comando &             # Executa em background
fg                    # Traz job para foreground
bg                    # Coloca job em background
jobs                  # Lista jobs
Ctrl+Z                # Pausa processo em execução
```

## 🌐 Rede

```bash
# Ver interfaces
ifconfig
ip a
ip addr show

# Ver conexões
netstat -tulpn
ss -tulpn

# Testar conectividade
ping google.com
ping -c 4 google.com

# Ver rota
traceroute google.com
tracepath google.com

# DNS
nslookup google.com
dig google.com
host google.com

# Download
curl -O https://site.com/arquivo.zip    # Download com nome original
curl -o nome.zip https://site.com/arquivo.zip
wget https://site.com/arquivo.zip
wget -O nome.zip https://site.com/arquivo.zip

# Portas
nc -zv localhost 80     # Verifica se porta está aberta
telnet localhost 80
lsof -i :8080           # Ver qual processo usa a porta
```

## 💾 Disco e Sistema

```bash
# Ver espaço em disco
df -h
df -H

# Ver tamanho de diretórios
du -sh pasta/
du -h --max-depth=1 /  # Tamanho por diretório (GNU; no BSD/macOS: du -h -d 1)

# Ver sistema
uname -a
hostname
cat /etc/os-release

# Ver memória
free -h
cat /proc/meminfo

# Ver CPU
lscpu
cat /proc/cpuinfo

# Desligar/Reiniciar
sudo shutdown -h now   # Desliga agora
sudo shutdown -r now   # Reinicia agora
sudo reboot            # Reinicia
sudo poweroff          # Desliga
```

## 📋 Compactação

* `Tar`

```bash
# Compactar
tar -cvf arquivo.tar pasta/           # Cria tar
tar -czvf arquivo.tar.gz pasta/       # Cria tar.gz (gzip)
tar -cjvf arquivo.tar.bz2 pasta/      # Cria tar.bz2 (bzip2)

# Descompactar
tar -xvf arquivo.tar
tar -xzvf arquivo.tar.gz
tar -xjvf arquivo.tar.bz2

# Ver conteúdo
tar -tvf arquivo.tar
```

### Zip/Unzip

```bash
# Compactar
zip arquivo.zip arquivo.txt
zip -r pasta.zip pasta/

# Descompactar
unzip arquivo.zip
unzip arquivo.zip -d destino/

# Ver conteúdo
unzip -l arquivo.zip
```

### Gzip/Gunzip

```bash
# Compactar
gzip arquivo.txt       # Cria arquivo.txt.gz

# Descompactar
gunzip arquivo.txt.gz
gzip -d arquivo.txt.gz
```

## 🚀 Alias e Variáveis

```bash
# Criar alias temporário
alias ll='ls -la'
alias gs='git status'
alias dc='docker-compose'

# Criar alias permanente
echo 'alias ll="ls -la"' >> ~/.zshrc
echo 'alias gs="git status"' >> ~/.zshrc
source ~/.zshrc

# Ver aliases
alias

# Variáveis de ambiente
export VARIAVEL=valor
echo $VARIAVEL
env               # Ver todas as variáveis
```

## 🆘 Ajuda

```bash
# Ajuda geral
man comando
comando --help
comando -h

# Exemplos
man ls
ls --help
```

## 📝 Aliases Úteis

Criar aliases no ~/.zshrc ou ~/.bashrc

```bash
# Navegação
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."
alias ~="cd ~"
alias home="cd ~"

# Listar arquivos
alias ll="ls -la"
alias l="ls -la"
alias ls-l="ls -l"
alias lsa="ls -la"
alias lsd="ls -ld */"

# Sistema
alias update="sudo apt update && sudo apt upgrade"
alias clean="sudo apt autoremove && sudo apt autoclean"
alias ports="netstat -tulpn | grep LISTEN"
alias mem="free -h"
alias disk="df -h"

# Processos
alias psg="ps aux | grep -v grep | grep -i"
alias top-mem="ps aux --sort=-%mem | head -10"
alias top-cpu="ps aux --sort=-%cpu | head -10"

# Segurança
alias chmod="chmod --preserve-root"
alias chown="chown --preserve-root"
alias rm="rm -i"

# Git
alias gs="git status"
alias ga="git add"
alias gc="git commit"
alias gp="git push"
alias gl="git log --oneline --graph --all"
```

## 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `pwd` | Ver diretório atual |
| `ls -la` | Listar arquivos |
| `cd pasta` | Navegar para pasta |
| `cat arquivo` | Ver conteúdo |
| `tail -f log` | Acompanhar log |
| `df -h` | Ver espaço em disco |
| `free -h` | Ver memória |
| `ps aux` | Ver processos |
| `sudo apt update` | Atualizar pacotes |
| `history` | Ver histórico de comandos |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Navegação** | `pwd`, `ls`, `cd` |
| **Arquivos** | `touch`, `cp`, `mv`, `rm`, `cat`, `less` |
| **Diretórios** | `mkdir`, `rmdir`, `rm -rf` |
| **Permissões** | `chmod`, `chown`, `chgrp` |
| **Busca** | `find`, `grep`, `which`, `whereis` |
| **Processos** | `ps`, `top`, `htop`, `kill` |
| **Rede** | `ping`, `curl`, `wget`, `netstat` |
| **Pacotes** | `apt`, `dnf`, `yum` |
| **Disco** | `df`, `du`, `free` |
| **Compactação** | `tar`, `zip`, `gzip` |
| **Ajuda** | `man`, `--help` |

## 📚 Referências

Documentação Oficial do Linux

Linux Command Line

TLDR Pages - Comandos simplificados

Pronto para usar Linux com confiança! 🐧🚀
