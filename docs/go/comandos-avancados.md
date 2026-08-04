# 📘 Comandos Avançados do Go

> Guia para usuários que já dominam o básico e querem concorrência, otimização, módulos avançados, geração de código e profiling

---

## 🧵 Concorrência

* `Goroutines`

```go
// ✅ Executar função concorrente
go processar(dados)

// Funcionamento simples
go func() {
    fmt.Println("rodando em paralelo")
}()
```

* `Channels`

```go
// Channel sem buffer (síncrono)
ch := make(chan int)
ch <- 42          // envia (bloqueia até receber)
v := <-ch         // recebe

// Channel com buffer
ch := make(chan string, 5)

// Fechar e iterar
close(ch)
for v := range ch {
    fmt.Println(v)
}
```

* `Select — múltiplos channels`

```go
select {
case v := <-c1:
    fmt.Println("c1:", v)
case v := <-c2:
    fmt.Println("c2:", v)
case <-time.After(1 * time.Second):
    fmt.Println("timeout")
}
```

* `WaitGroup`

```go
import "sync"

var wg sync.WaitGroup
wg.Add(3)

for i := 0; i < 3; i++ {
    go func(n int) {
        defer wg.Done()
        fmt.Println(n)
    }(i)
}

wg.Wait() // aguarda todas
```

* `Mutex`

```go
var mu sync.Mutex
contador := 0

// Modifica com lock
mu.Lock()
contador++
mu.Unlock()

// Ou com defer
mu.Lock()
defer mu.Unlock()
contador++
```

## 🧬 Interfaces e Genéricos

* `Interfaces (polimorfismo)`

```go
type Forma interface {
    Area() float64
}

type Retangulo struct{ L, A float64 }

func (r Retangulo) Area() float64 { return r.L * r.A }

func Descrever(f Forma) float64 {
    return f.Area()
}
```

* `Interfaces vazias / any`

```go
func RecebeQualquer(x any) {
    switch v := x.(type) {
    case int:
        fmt.Println("int:", v)
    case string:
        fmt.Println("string:", v)
    default:
        fmt.Println("outro")
    }
}
```

* `Genéricos (Go 1.18+)`

```go
// Função genérica
func Identidade[T any](v T) T { return v }

// Com constraint
func Soma[T ~int | ~float64](a, b T) T { return a + b }

// Em tipos
type Caixa[T any] struct { Valor T }
```

## ⚡ Profiling Avançado

* `pprof com http`

```go
// Importar net/http/pprof para profiling em runtime
import _ "net/http/pprof"

// acessar em http://localhost:6060/debug/pprof/
```

```bash
# Profile de aplicação rodando
go tool pprof http://localhost:6060/debug/pprof/heap
go tool pprof http://localhost:6060/debug/pprof/goroutine

# Omita o formato p/ entrar no shell interativo (top/list/tree)
go tool pprof cpu.out
# Saída única (não interativa):
go tool pprof -top cpu.out
go tool pprof -list "minhaFunc" cpu.out
```

* `Escape analysis`

```bash
# Ver onde o Go aloca (stack vs heap)
go build -gcflags="-m" .
go test -gcflags="-m -l" ./...

# ❌ Escapes para heap desnecessários aumentam GC
```

## 📦 Módulos Avançados

* `Versões esemânticas`

```bash
# Ver módulo e dependências
go list -m
go list -m -json all

# Ver documentação do módulo
go list -m -versions github.com/gin-gonic/gin
```

* `Crie sufixo /v2 para mudanças quebradas`

```go
// Em go.mod:
module github.com/usuario/projeto/v2
```

* `Arquivos separados por build tags`

```go
//go:build linux
package main
```

## 🔌 CGO e Bibliotecas Externas

```bash
# Build com CGO (chama código C)
CGO_ENABLED=1 go build -o app .

# Ver se usa CGO
go env CGO_ENABLED

# Build estático (menos dependências de libc)
CGO_ENABLED=0 go build .
```

## 🧪 Testes Avançados

* `Tabela de casos`

```go
func TestSoma(t *testing.T) {
    casos := []struct {
        a, b, esperado int
    }{
        {1, 2, 3},
        {0, 0, 0},
        {-1, 1, 0},
    }
    for _, c := range casos {
        if got := somar(c.a, c.b); got != c.esperado {
            t.Errorf("somar(%d,%d)=%d, esperado %d", c.a, c.b, got, c.esperado)
        }
    }
}
```

* `Mock e interface de fake`

```go
type DB interface{ Guardar(v string) error }

type FakeDB struct{ C int }

func (f *FakeDB) Guardar(v string) error { f.C++; return nil }

// Teste usa o FakeDB em vez da implementação real
```

* `TestMain`

```go
func TestMain(m *testing.M) {
    setup()
    code := m.Run()
    teardown()
    os.Exit(code)
}
```

* `Subtestes`

```go
func TestAPI(t *testing.T) {
    t.Run("criar", func(t *testing.T) { /* ... */ })
    t.Run("deletar", func(t *testing.T) { /* ... */ })
}
```

## 🔍 Ferramentas e Ecossistema Avançado

* `Ferramentas externas úteis`

```bash
# Hot reload
go install github.com/air-verse/air@latest
air

# Build multi-plataforma (usa GOOS/GOARCH)
GOOS=linux GOARCH=amd64 go build

# Listar dependências
go list -m all

# Verificar vulnerabilidades
govulncheck ./...
```

* `go generate`

```go
//go:generate stringer -type=Cor
type Cor int

const (
    Vermelho Cor = iota
    Verde
)
```

```bash
# Rodar geradores
go generate ./...
```

## 🧹 Gestão de Config e Build

* `ldflags para versão`

```bash
# Injetar variáveis no build
go build -ldflags "-X main.versao=1.0.0" .
```

```go
var versao string
```

* `Reduzir tamanho do binário`

```bash
go build -ldflags "-s -w" -o app .
# -s: sem symbols, -w: sem debug info
```

## 📋 Checklist de Go Avançado

| Tarefa | Comando/Ferramenta |
| ------ | ------------------ |
| Goroutines | `go func(){}()` |
| Profiling | `go tool pprof` |
| Race detector | `go test -race` |
| Cross-build | `GOOS=... GOARCH=... go build` |
| Vulnerabilidades | `govulncheck ./...` |
| Geração de código | `go generate` |
| Genéricos | `func F[T any]()` |
| CGO | `CGO_ENABLED` |

## 📚 Referências

* Go Blogs (Official)
* Effective Go
* Go Concurrency Patterns
* pkg.go.dev

Pronto para dominar Go em produção! 🐹🚀