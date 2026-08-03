# 📘 Comandos Básicos do Go

> Guia essencial para começar a usar Go e a ferramenta de linha de comando `go` no dia a dia

---

## 🔧 Instalação e Configuração

* `Instalar o Go`

```bash
# macOS (Homebrew)
brew install go

# Ubuntu/Debian
sudo apt install golang-go

# Windows (choco)
choco install golang

# Ou baixar o tarball oficial
wget https://go.dev/dl/go1.22.4.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.22.4.linux-amd64.tar.gz
```

* `Configurar ambiente`

```bash
# Ver versão
go version

# Variáveis de ambiente principais
go env GOPATH      # onde ficam módulos e binários (~/go)
go env GOROOT      # onde o Go está instalado
go env GOOS        # sistema alvo (linux, darwin, windows)
go env GOARCH      # arquitetura alvo (amd64, arm64)

# Adicionar binários ao PATH
export PATH="$PATH:$(go env GOPATH)/bin"
```

## 🚀 Iniciando um Projeto

* `Criar módulo`

```bash
# Criar módulo (com nome de repositório/módulo)
go mod init github.com/usuario/meu-projeto
go mod init exemplo.com/meuapp

# Estrutura típica
# .
# ├── go.mod          # módulo e dependências
# ├── go.sum          # hashes de dependências
# └── main.go         # ponto de entrada
```

* `Arquivo inicial`

```go
package main

import "fmt"

func main() {
    fmt.Println("Olá, Go!")
}
```

```bash
# Executar
go run main.go
```

## 🏗️ Compilação e Execução

* `Rodar e compilar`

```bash
# Executar sem gerar binário
go run main.go
go run .

# Compilar e gerar binário
go build
go build -o meu-binario .

# Compilar para outra plataforma
GOOS=linux GOARCH=amd64 go build -o app-linux .
GOOS=windows GOARCH=amd64 go build -o app.exe .
GOOS=darwin GOARCH=arm64 go build -o app-mac .

# Instalar binário no GOPATH/bin
go install .
```

* `Ver código compilado / vetores`

```bash
# Análise estática
go vet ./...

# Ver dependências no binário
go version -m binario
```

## 📦 Gerenciamento de Dependências

* `Adicionar dependências`

```bash
# Adicionar versão mais recente
go get github.com/gin-gonic/gin

# Adicionar versão específica
go get github.com/gin-gonic/gin@v1.9.1

# Adicionar e registrar no go.mod (via import)
go mod tidy

# Verificar módulos
go mod verify
```

* `Gerenciar módulos`

```bash
# Ver dependências do módulo
go list -m all
go mod graph

# Atualizar dependências
go get -u ./...
go get -u github.com/gin-gonic/gin

# Remover dependências não usadas
go mod tidy

# Ver módulos com versão desatualizada
go list -m -u all
```

## 🧪 Testes

* `Rodar testes`

```bash
# Rodar todos os testes do pacote
go test ./...

# Rodar testes com verbosidade
go test -v ./...

# Rodar um teste específico
go test -run TesteNome -v

# Rodar com cobertura
go test -cover ./...
go test -coverprofile=cobertura.out ./...
go tool cover -func=cobertura.out
go tool cover -html=cobertura.out
```

* `Testes com race detector`

```bash
# Detectar corridas (data races)
go test -race ./...
```

## 📊 Perfis e Benchmark

* `Benchmarks`

```go
// Em arquivo _test.go
func BenchmarkSomar(b *testing.B) {
    for i := 0; i < b.N; i++ {
        somar(2, 3)
    }
}
```

```bash
# Rodar benchmarks
go test -bench=.

# Com memória
go test -bench=. -benchmem
```

* `Profiling`

```bash
# CPU profile
go test -cpuprofile=cpu.out
go tool pprof cpu.out

# Heap profile
go test -memprofile=mem.out
go tool pprof mem.out
```

## 🛠️ Formatação e Lint

* `Formatação`

```bash
# Formatador oficial
gofmt -w arquivo.go
gofmt -l .          # lista arquivos não formatados

# Formatação automática no save
# (editores: format on save)

# Verificação de imports
goimports -w arquivo.go
```

* `Lint (externo)`

```bash
# golangci-lint (ferramenta agregada)
golangci-lint run ./...

# ou go vet já incluso
go vet ./...
```

## 📦 Estruturas e Comandos Úteis

* `Documentação`

```bash
# Ver docs locais
go doc fmt.Println
go doc time.Time

# Abrir docs do módulo no navegador
go doc -http=:6060

# Ver docs de um pacote externo
go doc github.com/gin-gonic/gin
```

* `Limpeza`

```bash
# Limpar cache de build
go clean -cache

# Remover binários
go clean -i
```

## 🆘 Ajuda

```bash
# Ajuda geral
go help
go help build
go help mod
```

## 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `go mod init` | Iniciar módulo |
| `go run .` | Executar |
| `go build` | Compilar |
| `go test ./...` | Rodar testes |
| `go vet ./...` | Análise estática |
| `gofmt -w .` | Formatar |
| `go mod tidy` | Organizar dependências |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Instalação** | `go version`, `go env` |
| **Módulos** | `go mod init`, `go get`, `go mod tidy` |
| **Build** | `go build`, `go run`, `go install` |
| **Testes** | `go test`, `-cover`, `-race` |
| **Perf** | `go test -bench`, `pprof` |
| **Fmt/Lint** | `gofmt`, `go vet`, `golangci-lint` |
| **Docs** | `go doc` |
| **Cross** | `GOOS`/`GOARCH` |

## 📚 Referências

* Go Documentation (docs oficial)
* Go By Example
* pkg.go.dev (documentação de pacotes)

Pronto para programar Go com confiança! 🐹🚀