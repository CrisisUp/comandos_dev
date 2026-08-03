# 📘 Comandos Básicos do PowerShell

> Guia essencial para começar a usar PowerShell no dia a dia

---

## 🔧 Conceitos Básicos

### O que é PowerShell?

- Shell e linguagem de script da Microsoft
- Baseado em .NET Framework/.NET Core
- Orientado a objetos (diferente do Bash que é texto)
- Comandos chamados de **Cmdlets** (Commandlets)
- Padrão: `Verbo-Substantivo` (ex: `Get-Process`, `Set-Location`)

### Verificar versão

```powershell
# Ver versão do PowerShell
$PSVersionTable.PSVersion

# Ver versão detalhada
Get-Host | Select-Object Version
```

## 📂 Navegação e Manipulação de Arquivos

- `Navegação`

```powershell
# Ver diretório atual
Get-Location
pwd

# Listar arquivos
Get-ChildItem
ls
dir

# Listar detalhado
Get-ChildItem -Force     # Inclui arquivos ocultos
ls -Force
Get-ChildItem -Recurse   # Lista recursivamente
ls -Recurse

# Listar com formato de tabela
Get-ChildItem | Format-Table Name, Length, LastWriteTime

# Mudar de diretório
Set-Location /home/usuario
cd /home/usuario
cd ~           # Ir para home
cd ..          # Voltar um diretório
cd -           # Voltar ao diretório anterior
```

## 📝 Manipulação de Arquivos

```powershell
# Criar arquivo vazio
New-Item arquivo.txt
New-Item arquivo.txt -ItemType File

# Criar arquivo com conteúdo
"Conteúdo" > arquivo.txt
Set-Content -Path arquivo.txt -Value "Conteúdo"

# Visualizar arquivos
Get-Content arquivo.txt
cat arquivo.txt

# Visualizar com opções
Get-Content arquivo.txt -Tail 10    # Últimas 10 linhas
Get-Content arquivo.txt -Head 10    # Primeiras 10 linhas
Get-Content arquivo.txt -Wait       # Acompanhar em tempo real (tail -f)

# Copiar arquivos
Copy-Item arquivo.txt backup.txt
cp arquivo.txt backup.txt
Copy-Item -Recurse pasta/ backup/   # Copia diretório recursivamente

# Mover/Renomear
Move-Item arquivo.txt pasta/
mv arquivo.txt pasta/
Rename-Item arquivo.txt novo_nome.txt
ren arquivo.txt novo_nome.txt

# Remover arquivos
Remove-Item arquivo.txt
rm arquivo.txt
Remove-Item -Recurse -Force pasta/  # Remove diretório e conteúdo (CUIDADO!)
rm -rf pasta/

# Verificar existência
Test-Path arquivo.txt
```

## 📂 Diretórios

```powershell
# Criar diretórios
New-Item -ItemType Directory -Path pasta
mkdir pasta
mkdir -p pasta/subpasta/subsubpasta

# Remover diretórios
Remove-Item -ItemType Directory pasta
rmdir pasta
Remove-Item -Recurse -Force pasta   # Remove recursivamente
```

## 📝 Editor de Texto (PowerShell ISE/VSCode)

```powershell
# Abrir no VSCode
code arquivo.txt

# Abrir no Notepad
notepad arquivo.txt

# Editar no PowerShell ISE
ise arquivo.txt

# Criar e editar (abre no bloco de notas)
New-Item arquivo.txt
notepad arquivo.txt
```

## 👥 Gerenciamento de Usuários

```powershell
# Ver usuário atual
whoami
$env:USERNAME

# Ver usuários do sistema
Get-LocalUser
Get-WmiObject -Class Win32_UserAccount

# Ver grupos
Get-LocalGroup

# Ver membros de um grupo
Get-LocalGroupMember -Group "Administradores"

# Adicionar usuário (Windows)
New-LocalUser -Name "usuario" -Password (Read-Host -AsSecureString "Senha")

# Remover usuário
Remove-LocalUser -Name "usuario"

# Alterar senha
$senha = Read-Host -AsSecureString "Nova senha"
Set-LocalUser -Name "usuario" -Password $senha
```

## 📊 Permissões (Windows)

```powershell
# Ver permissões de arquivo
Get-Acl arquivo.txt

# Alterar permissões
$acl = Get-Acl arquivo.txt
$permission = "usuario","FullControl","Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule $permission
$acl.SetAccessRule($accessRule)
Set-Acl arquivo.txt $acl

# Remover permissões
$acl.RemoveAccessRule($accessRule)
Set-Acl arquivo.txt $acl
```

## 🔍 Busca

```powershell
# Buscar arquivos
Get-ChildItem -Recurse -Filter "*.txt"
Get-ChildItem -Recurse -Include "*.txt"
Get-ChildItem -Recurse -Exclude "*.log"

# Buscar por nome
Get-ChildItem -Recurse -Name "*teste*"

# Buscar por tamanho
Get-ChildItem -Recurse | Where-Object { $_.Length -gt 1MB }

# Buscar em arquivos (Select-String = grep)
Select-String -Path "*.txt" -Pattern "texto"
Get-ChildItem -Recurse -Include "*.txt" | Select-String -Pattern "texto"

# Buscar com case insensitive
Select-String -Path "*.txt" -Pattern "texto" -CaseSensitive

# Buscar comandos
Get-Command *process*
Get-Command -Verb Get -Noun Process
```

## 📦 Gerenciamento de Pacotes (Windows)

- `Winget (Windows Package Manager)`

```powershell
# Instalar pacote
winget install nome_pacote

# Buscar pacote
winget search termo

# Listar pacotes instalados
winget list

# Atualizar pacote
winget upgrade nome_pacote

# Atualizar todos os pacotes
winget upgrade --all
```

- `Chocolatey (Alternativa)`

```powershell
# Instalar pacote
choco install nome_pacote

# Buscar pacote
choco search termo

# Listar pacotes instalados
choco list -lo

# Atualizar pacote
choco upgrade nome_pacote

# Atualizar todos os pacotes
choco upgrade all
```

- `Módulos PowerShell`

```powershell
# Instalar módulo
Install-Module -Name NomeModulo

# Buscar módulo
Find-Module -Name *termo*

# Listar módulos instalados
Get-InstalledModule

# Remover módulo
Uninstall-Module -Name NomeModulo
```

## 🎯 Processos

```powershell
# Ver processos
Get-Process
ps

# Ver processos com detalhes
Get-Process | Format-Table Name, CPU, MemorySize

# Ver processos específicos
Get-Process -Name "notepad"
Get-Process -Id 1234

# Ver processos com uso de memória
Get-Process | Sort-Object -Property WorkingSet -Descending

# Matar processos
Stop-Process -Name "notepad"
kill -Name "notepad"
Stop-Process -Id 1234
kill -Id 1234

# Iniciar processo
Start-Process -FilePath "notepad.exe"
Start-Process -FilePath "C:\Program Files\App\app.exe" -ArgumentList "/arg"

# Ver serviços
Get-Service
Get-Service -Name "spooler"
Get-Service | Where-Object { $_.Status -eq "Running" }

# Iniciar/Parar serviço
Start-Service -Name "spooler"
Stop-Service -Name "spooler"
Restart-Service -Name "spooler"
```

## 🌐 Rede

```powershell
# Ver configurações de rede
Get-NetIPAddress
Get-NetAdapter

# Ver IP
ipconfig
Get-NetIPConfiguration

# Ver conexões
Get-NetTCPConnection
netstat

# Testar conectividade
Test-Connection google.com
ping google.com
Test-Connection google.com -Count 4

# DNS
Resolve-DnsName google.com
nslookup google.com

# Download
Invoke-WebRequest -Uri "https://site.com/arquivo.zip" -OutFile "arquivo.zip"
curl -O "https://site.com/arquivo.zip"
wget "https://site.com/arquivo.zip"

# Portas (PowerShell 7+)
Test-NetConnection -Port 80 google.com
```

## 💾 Disco e Sistema

```powershell
# Ver espaço em disco
Get-PSDrive
Get-WmiObject -Class Win32_LogicalDisk

# Ver espaço (formatado)
Get-PSDrive -PSProvider FileSystem

# Ver memória
Get-WmiObject -Class Win32_OperatingSystem | 
Select-Object TotalVisibleMemorySize, FreePhysicalMemory

# Ver sistema
Get-ComputerInfo
systeminfo

# Ver versão do Windows
[Environment]::OSVersion.Version
Get-WmiObject -Class Win32_OperatingSystem

# Ver CPU
Get-WmiObject -Class Win32_Processor

# Desligar/Reiniciar
Stop-Computer
Restart-Computer
shutdown /s /t 0   # Desliga
shutdown /r /t 0   # Reinicia
```

## 📋 Compactação

- `Compactar/Descompactar (PowerShell 5+)`

```powershell
# Compactar (PowerShell 5+)
Compress-Archive -Path pasta/ -DestinationPath arquivo.zip
Compress-Archive -Path *.txt -DestinationPath arquivos.zip

# Descompactar
Expand-Archive -Path arquivo.zip -DestinationPath destino/
```

### Compactar/Descompactar (COM)

```powershell
# Usando Shell.Application (alternativa)
$shell = New-Object -ComObject Shell.Application
$zip = $shell.NameSpace("C:\caminho\arquivo.zip")
$folder = $shell.NameSpace("C:\pasta")
$folder.Items() | % { $zip.CopyHere($_) }
```

## 🔧 PowerShell Pipeline (Encadeamento)

```powershell

# Pipeline básico
Get-Process | Where-Object { $_.CPU -gt 100 }
Get-Service | Where-Object { $_.Status -eq "Running" }

# Pipeline com Select-Object
Get-Process | Select-Object Name, CPU, MemorySize
Get-Process | Select-Object -First 5

# Pipeline com Sort-Object
Get-Process | Sort-Object -Property CPU -Descending

# Pipeline com Group-Object
Get-Service | Group-Object -Property Status

# Pipeline com Export
Get-Process | Export-Csv -Path processos.csv
Get-Process | Out-File -FilePath processos.txt
```

## 🚀 Variáveis e Aliases

- `Variáveis`

```powershell
# Criar variável
$nome = "João"
$idade = 30
$lista = @(1, 2, 3, 4, 5)
$hash = @{ "nome" = "João"; "idade" = 30 }

# Ver variáveis
$nome
$idade
Get-Variable

# Remover variável
Remove-Variable -Name nome
```

- `Aliases`

```powershell
# Criar alias temporário
Set-Alias -Name ll -Value Get-ChildItem
Set-Alias -Name gs -Value Get-Service

# Criar alias permanente (perfil)
$profile_path = $PROFILE
"Set-Alias -Name ll -Value Get-ChildItem" >> $profile_path
"Set-Alias -Name gs -Value Get-Service" >> $profile_path

# Ver aliases
Get-Alias

# Remover alias
Remove-Item -Path Alias:ll
```

- `Funções`

```powershell
# Criar função
function Get-HelloWorld {
    Write-Host "Hello, World!"
}

function Get-Greeting {
    param($nome)
    Write-Host "Hello, $nome!"
}

# Usar função
Get-HelloWorld
Get-Greeting -nome "João"
```

## 🆘 Ajuda

```powershell
# Ajuda geral
Get-Help
Get-Help Get-Process
Get-Help Get-Process -Detailed
Get-Help Get-Process -Examples

# Ver detalhes do comando
Get-Command Get-Process
Get-Command -Syntax Get-Process

# Ver propriedades
Get-Process | Get-Member
```

## 📝 Aliases Úteis

Criar aliases no perfil PowerShell

```powershell
# Abrir perfil
notepad $PROFILE

# Adicionar aliases
Set-Alias ll Get-ChildItem
Set-Alias la Get-ChildItem
Set-Alias lsd Get-ChildItem -Directory
Set-Alias update "winget upgrade --all"
Set-Alias clean "winget upgrade --all"
Set-Alias mem Get-Memory
Set-Alias disk Get-Disk
Set-Alias psg "Get-Process | Where-Object { $_.Name -like '*$args*' }"
Set-Alias hist Get-History
Set-Alias tailf Get-Content -Wait
```

### Aliases para Git

```powershell
Set-Alias gs git status
Set-Alias ga git add
Set-Alias gc git commit
Set-Alias gp git push
Set-Alias gl git log --oneline --graph --all
```

## 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `pwd` | Ver diretório atual |
| `ls` | Listar arquivos |
| `cd pasta` | Navegar para pasta |
| `cat arquivo` | Ver conteúdo |
| `Get-Content -Wait` | Acompanhar log |
| `Get-PSDrive` | Ver espaço em disco |
| `Get-Process` | Ver processos |
| `Get-Service` | Ver serviços |
| `winget list` | Ver pacotes instalados |
| `Get-History` | Ver histórico de comandos |

## 🎯 Resumo dos Comandos

| Categoria | Comandos PowerShell | Equivalentes |
| --------- | ------------------- | ------------ |
| **Navegação** | `Get-Location`, `Set-Location`, `Get-ChildItem` | `pwd`, `cd`, `ls` |
| **Arquivos** | `New-Item`, `Copy-Item`, `Move-Item`, `Remove-Item` | `touch`, `cp`, `mv`, `rm` |
| **Conteúdo** | `Get-Content`, `Set-Content`, `Add-Content` | `cat`, `echo` |
| **Permissões** | `Get-Acl`, `Set-Acl` | `chmod`, `chown` |
| **Busca** | `Select-String`, `Get-Command` | `grep`, `which` |
| **Processos** | `Get-Process`, `Stop-Process` | `ps`, `kill` |
| **Rede** | `Test-Connection`, `Invoke-WebRequest` | `ping`, `curl` |
| **Pacotes** | `winget`, `choco`, `Install-Module` | `apt` |
| **Disco** | `Get-PSDrive`, `Get-WmiObject` | `df`, `du` |
| **Compactação** | `Compress-Archive`, `Expand-Archive` | `tar`, `zip` |
| **Ajuda** | `Get-Help`, `Get-Command` | `man`, `--help` |

## ⚡ PowerShell vs Bash (Comparação Rápida)

| Ação | PowerShell | Bash |
| ---- | ---------- | ---- |
| Listar arquivos | `ls` | `ls` |
| Listar detalhado | `ls -Force` | `ls -la` |
| Ver processos | `Get-Process` | `ps aux` |
| Buscar texto | `Select-String` | `grep` |
| Ver ajuda | `Get-Help` | `man` |
| Variáveis | `$var` | `$var` |
| Comentário | `#` | `#` |
| Pipeline | `\|` | `\|` |
| Condicional | `if ($x -eq 1) { }` | `if [ $x -eq 1 ]; then` |
| Loop | `foreach ($i in $lista) { }` | `for i in $lista; do` |
| Função | `function nome() { }` | `function nome() { }` |

## 🎯 Comandos Úteis para Windows

| Comando | Descrição |
| ------- | --------- |
| `Get-HotFix` | Ver atualizações instaladas |
| `Get-WindowsUpdateLog` | Ver logs do Windows Update |
| `Get-BitLockerVolume` | Ver status do BitLocker |
| `Get-ComputerInfo` | Informações do sistema |
| `Get-Disk` | Ver discos |
| `Get-Partition` | Ver partições |
| `Get-WmiObject -Class Win32_Bios` | Ver BIOS |
| `Get-WmiObject -Class Win32_LogicalDisk` | Ver discos lógicos |
| `Get-NetAdapter` | Ver adaptadores de rede |
| `Get-NetFirewallRule` | Ver regras de firewall |
| `Get-NetIPConfiguration` | Ver configuração de IP |

## 📚 Referências

Documentação Oficial do PowerShell
PowerShell Cheat Sheet
PowerShell GitHub

Pronto para usar PowerShell com confiança! 🖥️🚀
