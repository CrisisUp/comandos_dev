# 📘 Comandos Avançados do Linux

> Guia para usuários que já dominam o básico e querem controlar o sistema de forma mais profunda

---

## 📂 Permissões Avançadas

* `SUID / SGID / Sticky Bit`

```bash
# SUID (executa como dono do arquivo) - ex: passwd
chmod u+s arquivo
chmod 4755 arquivo

# SGID (herda grupo do diretório)
chmod g+s diretorio
chmod 2755 diretorio

# Sticky Bit (só dono remove) - ex: /tmp
chmod +t diretorio
chmod 1777 diretorio

# Ver permissões especiais
ls -l  # procura por s, S, t, T na posição de execução
```

* `ACL (Access Control Lists)`

```bash
# Ver ACLs
getfacl arquivo

# Adicionar usuário específico
setfacl -m u:usuario:rwx arquivo

# Adicionar grupo específico
setfacl -m g:grupo:rw arquivo

# Remover uma entrada
setfacl -x u:usuario arquivo

# Copiar ACLs de um arquivo para outro
getfacl origem | setfacl --set-file=- destino

# Remover todas as ACLs
setfacl -b arquivo
```

* `Umask`

```bash
# Ver umask atual
umask

# Definir umask (ex: 022 = novos arquivos 644, diretórios 755)
umask 022

# Modo simbólico
umask u=rwx,g=rx,o=rx
```

## 🔐 Segurança

* `AppArmor / SELinux`

```bash
# AppArmor
sudo aa-status                 # Status
sudo apparmor_parser -r perfil # Recarregar perfil
sudo aa-complain binario       # Modo complain (só loga)

# SELinux (Fedora/RHEL)
getenforce                     # Ver modo atual
sudo setenforce 0              # Permissive (temporário)
sudo setenforce 1              # Enforcing (temporário)
chcon -t httpd_sys_content_t arquivo  # Mudar contexto
```

* `Firewall (UFW)`

```bash
sudo ufw status verbose
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80
sudo ufw deny 23/tcp
sudo ufw allow from 192.168.1.100 to any port 3306
sudo ufw delete allow 80
sudo ufw status numbered
sudo ufw delete 3
```

* `Syslog / Journalctl`

```bash
# Journalctl (systemd)
journalctl -b                 # Desde o boot atual
journalctl -u nginx           # De um serviço
journalctl -f                 # Em tempo real
journalctl --since "2 hours ago"
journalctl -p err             # Apenas erros
journalctl -xe                # Últimas linhas + contexto

# Logs do kernel
dmesg
dmesg -T                      # Com timestamps legíveis
dmesg | tail -20
```

## 🚀 Processos e Agendamento

* `Agendamento (cron)`

```bash
# Editar cron do usuário
crontab -e

# Ver crons do usuário
crontab -l

# Remover cron
crontab -r

# Formato: minuto hora dia mês dia-da-semana comando
# Exemplos:
0 3 * * * /script/backup.sh              # 03:00 diário
*/5 * * * * /script/health.sh            # a cada 5 min
0 0 * * 0 /script/semanal.sh             # domingo 00:00
@reboot /script/start.sh                 # ao reiniciar

# Crons do sistema
sudo crontab -e
cat /etc/crontab
ls /etc/cron.d/
```

* `Agendamento (systemd timers)`

```bash
# Criar timer
sudo systemctl edit --force --full backup.timer

# Ver timers
systemctl list-timers

# Ativar timer
sudo systemctl enable --now backup.timer
```

* `Controle de processos`

```bash
# Monitorar árvore de processos
pstree -p

# Ver processo por porta
lsof -i :8080
ss -tulpn | grep 8080

# Limitar recursos de um processo
ulimit -a
ulimit -n 4096                # Máximo de arquivos abertos

# Processos em background com nohup
nohup comando > log.txt 2>&1 &

# Encerrar tudo de um usuário
pkill -u usuario
```

## 🔧 Avançado em Disco

* `LVM (Logical Volume Manager)`

```bash
# Ver volumes físicos, grupos e volumes lógicos
pvs && vgs && lvs

# Criar grupo de volumes
sudo vgcreate meu_vg /dev/sdb1 /dev/sdc1

# Criar volume lógico
sudo lvcreate -L 10G -n meu_lv meu_vg

# Estender volume lógico
sudo lvextend -L +5G /dev/meu_vg/meu_lv
sudo resize2fs /dev/meu_vg/meu_lv

# Reduzir volume lógico (sempre reduzir o filesystem ANTES do LV)
sudo umount /mnt/meu_lv
sudo e2fsck -f /dev/meu_vg/meu_lv
sudo resize2fs /dev/meu_vg/meu_lv 8G     # reduz o filesystem para o tamanho alvo
sudo lvreduce -L 8G /dev/meu_vg/meu_lv   # depois reduz o volume lógico
sudo mount /mnt/meu_lv
```

* `Montagens avançadas`

```bash
# Montar com opções específicas
sudo mount -o remount,rw /particao
sudo mount -o loop imagem.iso /mnt/iso
sudo mount -t nfs servidor:/export /mnt/nfs

# Montagens persistentes
# /etc/fstab:
# UUID=xxx /mnt/dados ext4 defaults 0 2
sudo mount -a

# Ver pontos de montagem
findmnt
mount | column -t
```

* `Encryption (LUKS)`

```bash
# Criar volume criptografado
sudo cryptsetup luksFormat /dev/sdb1
sudo cryptsetup open /dev/sdb1 meu_disco
sudo mkfs.ext4 /dev/mapper/meu_disco
sudo mount /dev/mapper/meu_disco /mnt

# Fechar
sudo umount /mnt
sudo cryptsetup close meu_disco
```

## 🌐 Rede Avançada

* `ip` (substituto do ifconfig)

```bash
# Endereços
ip addr show
ip addr add 192.168.1.100/24 dev eth0
ip addr del 192.168.1.100/24 dev eth0

# Rotas
ip route show
ip route add default via 192.168.1.1
ip route del default

# Configuração de interfaces
ip link set eth0 up
ip link set eth0 down

# Tunel / bridge
ip link add br0 type bridge
ip link set eth0 master br0
```

* `Captura e análise de pacotes`

```bash
# tcpdump
sudo tcpdump -i eth0
sudo tcpdump -i eth0 port 80
sudo tcpdump -i eth0 host 8.8.8.8
sudo tcpdump -i eth0 -w captura.pcap
sudo tcpdump -i eth0 -r captura.pcap
sudo tcpdump -i eth0 -nn -s 0 -A port 443

# traceroute / mtr
traceroute -T -p 443 google.com
mtr -rwc 10 google.com
```

* `DNS e certificados`

```bash
# Consulta DNS com tipo específico
dig google.com MX
dig @8.8.8.8 google.com A +short

# Testar handshake TLS
openssl s_client -connect google.com:443 -servername google.com

# Ver certificado
echo | openssl s_client -connect google.com:443 2>/dev/null | openssl x509 -noout -dates
```

## 🔍 Texto e Streams

* `Processamento de texto`

```bash
# awk - processa colunas
awk '{print $1, $3}' arquivo
awk -F: '{print $1}' /etc/passwd
awk '$3 > 100 {print $1}' arquivo
awk 'NR==1,NR==10 {print}' arquivo

# sed - substituição/edição
sed 's/antigo/novo/' arquivo        # primeira ocorrência
sed 's/antigo/novo/g' arquivo       # todas
sed -i 's/antigo/novo/g' arquivo    # in-place
sed -n '10,20p' arquivo             # intervalo de linhas
sed '/^$/d' arquivo                 # remove linhas vazias

# xargs - encadeia saída
find . -name "*.log" | xargs rm
echo "a b c" | xargs -n1
find . -type f | xargs -I{} cp {} /backup/
```

* `Streams e redirecionamento`

```bash
# Redirecionar stdout e stderr
comando > saida.txt 2>&1
comando &>> saida.txt              # append ambos

# Pipe com tee (salva e mostra)
comando | tee saida.txt
comando | tee -a saida.txt

# Process substitution
diff <(ls dir1) <(ls dir2)
while read linha; do echo "$linha"; done < arquivo.txt
```

* `Screen / Tmux`

```bash
# tmux - sessões
tmux new -s trabalho
tmux detach                  # Ctrl+b d
tmux attach -t trabalho
tmux list-sessions
tmux kill-session -t trabalho

# Pane split
tmux split-window -h        # Ctrl+b %
tmux split-window -v        # Ctrl+b "
tmux select-pane -R         # Ctrl+b setas
```

## ⚙️ Kernel e Sistema

```bash
# Parâmetros do kernel
sysctl vm.swappiness
sysctl -w vm.swappiness=10
sysctl -a | grep net.ipv4.ip_forward

# Módulos
lsmod
modinfo modulo
sudo modprobe modulo
sudo rmmod modulo

# Informações de hardware
lshw -short
lsblk
lscpu
lsusb
```

## 📝 Aliases e Atalhos Avançados

* `Funções no shell`

```bash
# Função no ~/.zshrc ou ~/.bashrc
mkcd() { mkdir -p "$1" && cd "$1"; }
backup() { rsync -av --delete "$1/" "$2/"; }

# Exportar para subprocessos
export -f mkcd
```

* `Uso de rsync`

```bash
# Sincronizar local
rsync -av origem/ destino/

# Sincronizar remoto
rsync -avz -e ssh origem/ usuario@host:/destino/

# Delete arquivos que não existem mais na origem
rsync -av --delete origem/ destino/

# Backup incremental com hardlinks
rsync -av --link-dest=../backup-anterior origem/ novo-backup/
```

## 📋 Checklist de Operação Avançada

| Tarefa | Comando |
| ------ | ------- |
| Ver permissões especiais | `ls -l` + `stat arquivo` |
| Ver ACLs | `getfacl arquivo` |
| Agendar tarefa | `crontab -e` |
| Ver logs do boot | `journalctl -b` |
| Ver processos | `pstree -p` / `ps aux` |
| Ver porta em uso | `ss -tulpn` / `lsof -i :8080` |
| Capturar pacotes | `sudo tcpdump -i eth0` |
| Consultar DNS | `dig` |
| Sessões persistentes | `tmux` |

## 📚 Referências

* `man` de cada comando
* TLDR Pages
* Arch Linux Wiki (referência avançada)

Pronto para operar Linux com profundidade! 🐧🚀
