# 📘 Boas Práticas no Go

> Guia de boas práticas para escrever Go idiomático, eficiente, seguro e de fácil manutenção

---

## 🏗️ Estrutura e Organização

* `Layout de projeto`

```
meu-projeto/
├── cmd/            # binários (main)
│   └── app/
│       └── main.go
├── internal/       # código privado (não importável externamente)
│   └── service/
├── pkg/            # código público reutilizável
├── api/            # definições de API/contratos
└── go.mod
```

* `Regras de empacotamento`

```go
// ✅ Um propósito por pacote, nomes curtos
package service

// ❌ Pacote genérico como "utils" ou "helpers"
```

## ✍️ Estilo e Convenções

* `Formatador oficial sempre`

```bash
# ✅ gofmt resolve a maioria das divergências
gofmt -w .
# Em CI: verificar que nada diverge
gofmt -l . && go vet ./...
```

* `Nomes idiomáticos`

```go
// ✅ curto, claro
func Soma(a, b int) int { return a + b }

// ✅ inicialização para erro
err := processar()
if err != nil {
    return err
}

// ❌ Não use snake_case em Go
// ❌ Não use "this"/"self"
```

* `Comentários em docstring`

```go
// Soma retorna a soma de a e b.
func Soma(a, b int) int { return a + b }
```

## 🔒 Tratamento de Erros

* `Erro é valor, não exceção`

```go
// ✅ Sempre checar
f, err := os.Open("arquivo.txt")
if err != nil {
    return fmt.Errorf("abrir arquivo: %w", err) // wrap com %w
}
defer f.Close()

// ❌ Ignorar err com _
// ❌ Panic/Recover no fluxo normal
```

* `Crie erros descritivos`

```go
var ErrNaoEncontrado = errors.New("registro não encontrado")

if !ok {
    return ErrNaoEncontrado
}
```

## 🧵 Concorrência Segura

* `Não compartilhe memória; comunique via channels`

```go
// ✅ Goroutines coordenadas por channel
jobs := make(chan Job, 10)
go worker(jobs)
```

* `Evite corridas de dados`

```bash
# ✅ Sempre rode com -race em testes
go test -race ./...
```

* `Use Mutex para estado compartilhado simples`

```go
// ✅ Ou use atomic
var contador atomic.Int64
contador.Add(1)
```

## 📦 Dependências

* `go.mod organizado`

```bash
# ✅ Sempre rodar tidy antes de commit
go mod tidy

# ✅ Verificar versões pinadas onde importa
go get github.com/x/y@v1.2.3
```

* `Minimize dependências`

```go
// ✅ Biblioteca padrão cobre muito
// ❌ Adicionar um framework para um caso simples
```

## 🧪 Testes de Qualidade

* `Teste tabela-driven`

```go
func TestSoma(t *testing.T) {
    casos := []struct{ a, b, want int }{
        {1, 2, 3},
        {2, 2, 4},
    }
    for _, c := range casos {
        if got := Soma(c.a, c.b); got != c.want {
            t.Errorf("Soma(%d,%d) = %d, want %d", c.a, c.b, got, c.want)
        }
    }
}
```

* `Teste com cobertura adequada`

```bash
go test -cover ./...
go test -coverprofile=cover.out ./... && go tool cover -html=cover.out
```

## ⚡ Performance

* `Evite alocações desnecessárias`

```go
// ✅ Reuse slices/strings com buffer
var buf bytes.Buffer
for _, s := range linhas { buf.WriteString(s) }

// ✅ Pré-alocar quando sabe o tamanho
s := make([]int, 0, 100)
```

* `Conheça a escape analysis`

```go
// ✅ Prefira valor em vez de ponteiro quando não precisa
type Point struct{ X, Y int }
func Area(p Point) int { return p.X * p.Y } // por valor
```

* `Pipelines com iteradores`

```go
// ✅ Transforme em um único loop quando simples
```

## 🧭 Interface e Design

* `Aceite interfaces, retorne structs`

```go
// ✅
func Save(db interface{ Save(v any) error }, v any) error

// ❌ Não defina interface no consumidor sem necessidade
```

* `Pequenas interfaces`

```go
type Reader interface { Read(p []byte) (int, error) }
```

## 🔐 Segurança

* `Validação de entrada`

```go
func ServeHTTP(w http.ResponseWriter, r *http.Request) {
    id := r.URL.Query().Get("id")
    if id == "" {
        http.Error(w, "id obrigatório", http.StatusBadRequest)
        return
    }
    // valide formato/tipo antes de usar
}
```

* `Não exponha stack em produção`

```go
// ✅ Log de erros sem dados sensíveis
log.Printf("erro ao processar: %v", err)
```

## 📋 Checklist de Boas Práticas Go

* ✅ `gofmt` + `go vet` sempre
* ✅ Testes com `-race`
* ✅ `go mod tidy` antes de commit
* ✅ Erros como valor, sem panic
* ✅ Concorrência por channels + sync
* ✅ Nomes curtos e claros
* ✅ Cobertura de testes para código crítico
* ✅ Validação de entrada de usuários

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Formatação | `gofmt` obrigatório |
| Erros | Retornar e tratar; sem ignorar |
| Concorrência | `go test -race` |
| Deps | `go mod tidy`, mínimas |
| Testes | Tabela-driven + cobertura |
| Nomes | Curto e descritivo |
| Interfaces | Pequenas, "aceite interface" |

## 📚 Referências

* Effective Go
* Go Style Guide (Google)
* Go Code Review Comments
* The Go Blog

Pronto para escrever Go com boas práticas! 🐹🚀