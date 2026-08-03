# 📘 Boas Práticas no PowerShell

> Guia de boas práticas para escrever scripts PowerShell seguros, legíveis e eficientes

---

## 📝 Sintaxe e Legibilidade

### 1. Nomeie cmdlets com Verbo-Substantivo

```powershell
# ✅ Claro e idiomático
Get-Process
Stop-Service -Name "spooler"
Set-Location -Path $home

# ❌ Evite nomes vagos ou comandos próprios sem padrão
# (um comando "DoIt" não comunica intenção)
```

### 2. Use parâmetros nomeados quando ajudar

```powershell
# ✅ Self-documenting
Get-ChildItem -Path C:\Users -Filter "*.log" -Recurse

# ❌ Posicional demais dificulta a leitura
gci C:\Users *.log -r
```

### 3. Indentação consistente

```powershell
# ✅ Bloco bem indentado
Get-Process |
    Where-Object { $_.CPU -gt 100 } |
    Sort-Object CPU -Descending |
    Select-Object -First 5

# ❌ Pipeline em uma linha gigante
```

## 🔒 Segurança

### 1. Nunca use senhas em texto puro

```powershell
# ❌ NUNCA FAÇA ISSO
$senha = "123456"
Invoke-Command -ComputerName x -Credential "usuario","123456"

# ✅ Use SecureString e credencial
$secure = Read-Host "Senha" -AsSecureString
$cred = New-Object System.Management.Automation.PSCredential("usuario", $secure)

# ✅ Ou importe de arquivo criptografado
$cred = Import-Clixml "credenciais.xml"
```

### 2. Evite comandos destrutivos sem salvaguardas

```powershell
# ❌ Remove-Item -Recurse sem WhatIf
Remove-Item -Path .\pasta -Recurse

# ✅ Sempre possível testar antes
Remove-Item -Path .\pasta -Recurse -WhatIf

# ✅ E use -Confirm para pedir confirmação
Remove-Item -Path .\pasta -Recurse -Confirm
```

## 🧪 Testes e Debug

### 1. Use Common Parameters de simulação

```powershell
# -WhatIf: mostra o que faria sem fazer
Stop-Service -Name spooler -WhatIf

# -Confirm: pede confirmação
Remove-Item .\x.txt -Confirm
```

### 2. Log de execução

```powershell
# ✅ Registrar passos
Write-Host "[INFO] Iniciando..." -ForegroundColor Cyan
Write-Verbose "Copiando $arquivo"
Start-Transcript -Path ".\log.txt"
# ... execução ...
Stop-Transcript
```

### 3. Tratamento de erros explícito

```powershell
try {
    Get-Item C:\nao_existe.txt -ErrorAction Stop
} catch {
    Write-Error "Falha: $_"
    exit 1
} finally {
    Cleanup
}
```

## 📦 Organização de Scripts

### 1. Sempre use param()

```powershell
<#
.SYNOPSIS
  Copia backups.
.DESCRIPTION
  Descrição detalhada...
.PARAMETER Source
  Caminho de origem.
#>
param(
    [Parameter(Mandatory=$true)]
    [string]$Source,

    [string]$Destination = ".\backup"
)
```

### 2. Funções reutilizáveis, não código repetido

```powershell
function Test-PortOpen {
    param([string]$HostName, [int]$Port)
    Test-NetConnection -ComputerName $HostName -Port $Port -InformationLevel Quiet
}

if (Test-PortOpen "servidor" 3389) { ... }
```

### 3. Não hardcode caminhos

```powershell
# ❌ Caminho fixo quebra em outra máquina
$p = "C:\meu\usuario\arquivo.txt"

# ✅ Use variáveis do sistema
$p = Join-Path $env:USERPROFILE "arquivo.txt"
```

## 🔧 Performance

```powershell
# ✅ Filtrar o mais cedo possível (Where antes de Select/Export)
Get-Process | Where-Object { $_.CPU -gt 100 } | Select-Object Name

# ✅ Evitar Get-ChildItem -Recurse sem necessidade
Get-ChildItem . -Recurse -Include "*.log"

# ✅ Agrupar operações de I/O
Get-Service | Where-Object Status -eq 'Running' | ForEach-Object {
    Start-Process ...
}
```

## 🧱 Estrutura de Arquivos e Perfil

- Mantenha funções em módulos (`.psm1`), não tudo no perfil.
- Coloque paths e credenciais em arquivos de config separados (`.psd1`/XML).
- Versionar scripts (git) para acompanhar mudanças.

## 📋 Checklist de Boas Práticas PowerShell

* 📝 Nomes Verbo-Substantivo e parâmetros nomeados
* 🔒 Sem senhas em texto puro; SecureString/Credential
* 🧪 Usar -WhatIf / -Confirm antes de destrutivos
* 🧮 Tratar erros com try/catch
* 📦 Scripts com `param()` e docstrings
* 🔧 Filtrar cedo em pipelines
* 🗂️ Caminhos dinâmicos ($env:, Join-Path)
* 📜 Logging em operações importantes

## 🎯 Resumo

| Prática | Recomendação |
|-------- |------------- |
| Nomenclatura | Verbo-Substantivo |
| Segredos | SecureString / Credencial |
| Comandos destrutivos | -WhatIf / -Confirm |
| Erros | try/catch/finally |
| Scripts | param() + comentários |
| Caminhos | Dinâmicos ($env:) |
| Pipeline | Filtrar cedo |

## 📚 Referências

* PowerShell Best Practices
* PowerShell Scripting Guide
* Microsoft Docs - PowerShell
* PSScriptAnalyzer (lint)

Pronto para automatizar Windows com boas práticas! 🖥️🚀