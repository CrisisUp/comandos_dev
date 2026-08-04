# 📘 Comandos Avançados do PowerShell

> Guia para usuários que já dominam o básico e querem explorar scripting, .NET, remoting e automação

---

## 🧠 Fundamentos Avançados

### Pipelines e objetos

```powershell
# Pipeline executa agrupado após cada comando
'abc' | ForEach-Object { $_.ToUpper() }

# Passar objetos (não texto) entre comandos
Get-Process | Where-Object { $_.CPU -gt 100 } | Select-Object Name, CPU

# Inserir erro no meio do pipeline
Get-Process | Sort-Object CPU -Descending
```

### Script blocks e clausuras

```powershell
# Script block (com param() para receber argumentos nomeados)
$sb = { param($a, $b) $a + $b }
& $sb -a 1 -b 2
. $sb

# Chamar com invoke
Invoke-Command -ScriptBlock { 'oi' }

# Retornar valor de função
function Get-Double([int]$n) { return $n * 2 }
```

## 📦 Módulos e Scripts

### Carregar módulos

```powershell
# Listar módulos disponíveis
Get-Module -ListAvailable

# Importar módulo
Import-Module -Name Az
Import-Module -Force -Name Meu

# Ver funções e cmdlets
Get-Command -Module Az | Where-Object { $_.CommandType -in 'Function','Cmdlet' }
Get-Module -Name Az | Format-List
```

### Criar módulos e scripts

```powershell
# Criar um arquivo .psm1
New-Module -Name meu_modulo -ScriptBlock {
    function Get-Teste { 'Valor retornado' }
    Export-ModuleMember -Function *
} | Import-Module -Force

# Manifest
New-ModuleManifest -Path .\mod.psd1 -RootModule mod.psm1
```

## 🔍 Expressões e .NET

```powershell
# Criar objetos .NET
Add-Type 'public class Foo { public static void MainAnd() { } }'

# Usar .NET methods
[System.Net.Dns]::GetHostAddresses('google.com')
[System.IO.Path]::GetFileName('C:\test\desc.txt')
[System.Math]::Max(1, 2)

# Criar tipos
[pscustomobject]@{ Nome = 'X'; Idade = 30 }
[hashtable]@{ a = 1 }
```

* `Tipos e conversões`

```powershell
# Tipos primitivos
[int]$n = 42
[string]$s = 'texto'
[datetime]$d = Get-Date
[bool]$b = $true

# Casting
[int]'42'
[string]123
[double]'1.5'

# Array e hashtable de acesso
$arr = @(1,2,3)
$arr[0]
$hash = @{ a = 10 }
$hash['a']
```

## 🔌 PowerShell Remoting

```powershell
# Habilitar remoting (precisa de admin)
Enable-PSRemoting -Force

# Conexão interativa
Enter-PSSession -ComputerName server01
Enter-PSSession -Session $s

# Executar remoto
Invoke-Command -ComputerName server01 -ScriptBlock { Get-Service }
Invoke-Command -ComputerName server01 -Credential (Get-Credential)

# Múltiplos computadores
$computers = 'pc1','pc2','pc3'
Invoke-Command -ComputerName $computers -ScriptBlock { systeminfo }

# Remoting com sessões e sem novas conexões
$s = New-PSSession -ComputerName server01
Invoke-Command -Session $s -ScriptBlock { Get-Process }
Remove-PSSession $s
```

## 🔍 Gerenciamento de Serviços e COM

```powershell
# Serviços
Get-Service | Sort-Object Status,Name
Get-Service 'spooler' | Select-Object Status,Name,DisplayName

# Windows Management Instrumentation (WMI via CIM)
Get-CimInstance Win32_Process
Get-CimInstance Win32_Service -Filter "State='Running'"
Get-CimInstance -ClassName Win32_OperatingSystem

# COM Objects
$excel = New-Object -ComObject Excel.Application
$excel.Visible = $true
```

## 🖥️ Área de Trabalho e Ambiente

```powershell
# Ambiente
$env:PATH
$env:USERNAME
[System.Environment]::GetEnvironmentVariable('TEMP')

# Triggers de scripts
Get-TaskScheduler
```

## 🔐 Segurança e Credenciais

```powershell
# Credenciais comentadas (sem plain text)
$secure = Read-Host "Senha" -AsSecureString
$cred = New-Object System.Management.Automation.PSCredential('usuario', $secure)

# Salvar de forma segura
$cred | Export-Clixml cred.xml
$cred2 = Import-Clixml cred.xml

# Permissões em arquivos
Get-Acl .\arquivo.txt | Format-List
```

## 🧮 Tratamento de Erros

```powershell
# Coleta de erros (try/catch/finally)
try {
    Get-Item C:\inexistente.txt
} catch {
    Write-Host "Erro: $_"
} finally {
    Write-Host "Fim"
}

# Erros não-terminantes
Get-ChildItem -ErrorAction SilentlyContinue
Get-ChildItem -ErrorAction Stop
```

## 🧬 Técnicas de Formatação e Saída

```powershell
# Formatos
Get-Process | Format-Table -AutoSize
Get-Process | Format-List -Property *
Get-Process | ConvertTo-Json
Get-Process | ConvertTo-Csv -NoTypeInformation
Get-Process | Export-Clixml -Path processos.xml

# Falar de objeto
Get-Process | Select-Object -ExpandProperty ProcessName
Get-Process | Measure-Object -Property CPU -Sum -Average

# Splatting
$params = @{ Path = '.\x.txt'; Force = $true }
Get-Item @params
```

## 🔁 Loops e Fluxo

```powershell
# foreach
foreach ($i in 1..5) { Write-Host $i }

# For
for ($i=0; $i -lt 5; $i++) { Write-Host $i }

# While
$i = 0
while ($i -lt 3) { Write-Host $i; $i++ }

# Do/While
$i = 0
do { Write-Host $i; $i++ } while ($i -lt 2)

# Switch
switch ('a') {
    'a' { 'acertou' }
    default { 'outro' }
}
```

## 🔍 PowerShell 7+ (Core)

```powershell
# Operadores ternários
($true) ? 'sim' : 'não'

# Null-coalescing
$valor ?? 'padrão'
$valor ??= 'fallback'

# Operador pipeline ternary
'texto' ?? 'algo' ? 'a' : 'b'
```

## 📜 Profile e Personalização

```powershell
# Carregar perfil
$PROFILE
notepad $PROFILE

# Adicionar prompt personalizado
function prompt { "PS $(Get-Location)> " }

# Aliases persistentes via profile
Set-Alias ll Get-ChildItem
```

## 📋 Checklist de PowerShell Avançado

| Tarefa | Comando |
| ------ | ------- |
| Conectar remoto | `Enter-PSSession -ComputerName X` |
| Executar remoto | `Invoke-Command -ComputerName X -ScriptBlock {...}` |
| Listar módulos | `Get-Module -ListAvailable` |
| Usar .NET | `[System.IO.Path]::GetFileName(...)` |
| Tratar erros | `try { } catch { } finally { }` |
| Splatting | `$params = @{...}; Cmdlet @params` |
| Mensurar | `... | Measure-Object -Sum` |
| Sessão remota | `New-PSSession` |

## 📚 Referências

* Documentação Oficial do PowerShell
* `Get-Help` para cada cmdlet
* PowerShell Remoting Guide
* Exemplos do GitHub PowerShell

Pronto para dominar o PowerShell! 🖥️🚀