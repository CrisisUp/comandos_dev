# 📘 Boas Práticas no Rust

> Guia de boas práticas para escrever Rust seguro, idiomático, performático e de fácil manutenção

---

## ✅ Propriedade e Borrowing

* `Evite clones desnecessários`

```rust
// ❌ Clona quando poderia só referenciar
fn processa(s: String) {
    // usa s sem clonar externamente
}

// ✅ Use &str como parâmetro quando não precisa ser dono
fn processa(s: &str) {
    // ...
}
processa(&minha_string);
```

* `Uso de & e &mut corretos`

```rust
// ✅ Referência imutável para leitura
fn ler(v: &Vec<i32>) -> i32 { v.len() as i32 }

// ✅ Mutable só quando precisa mudar
fn adicionar(v: &mut Vec<i32>, x: i32) { v.push(x); }

// ❌ Não use clonar/allocar só por preguiça de borrow
```

* `Borrow checker a seu favor`

```rust
// Divida o problema em escopos (permitir mut/immut alternado)
let mut v = vec![1, 2, 3];
for x in &v { println!("{x}") }        // borrow imutável
v.push(4);                              // agora mutável
```

## 📝 Idioma e Convenções

* `Tratamento de erros idiomático`

```rust
fn ler_arquivo(caminho: &str) -> Result<String, std::io::Error> {
    std::fs::read_to_string(caminho)
}

// ✅ Propague com ?
fn main() -> Result<(), Box<dyn std::error::Error>> {
    let c = ler_arquivo("x.txt")?;
    println!("{c}");
    Ok(())
}
```

* `Nomenclatura`

```rust
// ✅ snake_case para funções/variáveis, CamelCase para tipos
fn calcular_total() {}          // função
let quantidade = 10;             // variável
struct ConfigUsuario {}          // tipo

// ❌ Nomes vagos: data, temp, coisa
```

* `Imutabilidade por padrão`

```rust
// ✅ Prefira variáveis imutáveis
let config = ler_config();

// Só faça mut quando necessário
let mut acumulador = 0;
```

## 🧩 Structs, Enums e Pattern Matching

* `Enum como coração do domínio`

```rust
enum Status {
    Pendente,
    EmAndamento,
    Concluido { data: String },
}

fn descrever(s: &Status) -> &str {
    match s {
        Status::Pendente => "aguardando",
        Status::EmAndamento => "processando",
        Status::Concluido { .. } => "pronto",
    }
}
```

* `Usar newtype para evitar erros`

```rust
// ✅ Newtype restringe erros de unidade
struct Cm(f64);
struct Metros(f64);

// ❌ misturar f64 livres leva a empilhar unidades erradas
```

## 🔒 Segurança

* `Evite unwrap/expect em produção`

```rust
// ❌ Pode panica em erro
let v = dados.unwrap();

// ✅ Trate ou propague
let v = dados.ok_or("dados ausentes")?;
```

## 🚀 Performance

* `Poupar alocações`

```rust
// ✅ Vec com capacidade pré-reservada
let mut v = Vec::with_capacity(1000);
for i in 0..1000 { v.push(i); }

// ✅ Buffer de String reutilizável
let mut out = String::new();
for s in linhas { out.push_str(s); }

// ❌ concatenar strings no loop com + (alocação constante)
```

* `Iteradores em vez de loops manuais`

```rust
// ✅ Escada idiomática e eficiente
let soma: i32 = dados.iter().map(|x| x * 2).filter(|x| x > 10).sum();

// ❌ loop indexado verboso quando um iterator serve
```

* `Evite `to_string()` quando `&str` basta`

## 🧪 Testes

```rust
// ✅ Testes unitários junto ao código

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn soma_correta() {
        assert_eq!(somar(2, 3), 5);
    }

    #[test]
    fn retorna_erro() {
        assert!(dividir(1.0, 0.0).is_err());
    }
}

// ✅ Testes de integração em tests/ integração
```

## 📦 Cargo e Dependências

* `Mantenha Cargo.toml limpo`

```toml
[dependencies]
# versão mínima razoável, sem pin grande se não precisar
serde = "1.0"
# features mínimas
tokio = { version = "1", features = ["rt-multi-thread"] }
```

* `Não ignore o code review de deps`

```bash
cargo audit     # vulnerabilidades
cargo tree      # transparências
```

## 🧘 Layout de Projetos

```
meu-crate/
├── src/
│   ├── lib.rs        # API pública
│   ├── main.rs       # (se binário)
│   └── modules/      # separação por módulo
├── tests/            # integração
├── benches/          # benchmark
└── Cargo.toml
```

## 📋 Checklist de Boas Práticas Rust

* ✅ Borrow checke favorável; evita clones
* ✅ `Result`/`Option` em vez de panic/`unwrap`
* ✅ Enum para estados, newtype para unidades
* ✅ Iteradores e `with_capacity`
* ✅ Testes com `#[cfg(test)]`
* ✅ Sem `#![allow(unused)]` cegos
* ✅ `cargo clippy` e `cargo fmt` no CI
* ✅ Documentação (`//!`, `///`) para código público

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Owneship | Borrow > clone |
| Erros | `Result`/`?`; sem `unwrap` em prod |
| Tipos | Enum para alternativas |
| Perf | Prelocação, iterators |
| Testes | `#[test]` junto ao código |
| Ferramentas | Clippy + fmt no CI |
| Dependências | Verificar com `cargo deny` |

## 📚 Referências

* The Rust Book (Cap 4 Ownership, 5, 8...)
* Rust Clippy lint collection
* Rust API Guidelines
* Effective Rust

Pronto para escrever Rust com boas práticas! 🦀🚀