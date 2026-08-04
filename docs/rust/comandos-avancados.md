# 📘 Comandos Avançados do Rust

> Guia para usuários que já dominam o básico e querem workspaces, features, otimização, concorrência e unsafe

---

## 🏗️ Cargo Avançado

### Workspaces

```toml
# Cargo.toml (raiz do workspace)
[workspace]
members = [
    "crates/parser",
    "crates/compiler",
    "apps/cli",
]
resolver = "2"

# Cargo.toml de cada crate
# (sem [package] na raiz; cada membro tem o seu)
```

```bash
# Comandos aplicam a todo o workspace
cargo build --workspace
cargo test --workspace
cargo fmt --all

# Usar --package para um membro
cargo run -p apps/cli
cargo build -p crates/parser
```

### Features

```toml
# Cargo.toml
[features]
default = ["std"]
std = []
async = ["dep:tokio"]
extra = ["serde", "serde/derive"]

[dependencies]
tokio = { version = "1", optional = true }
serde = { version = "1", optional = true, features = ["derive"] }
```

```bash
# Ativar feature
cargo build --features async

# Ativar múltiplas
cargo build --features "async extra"

# Desativar padrão
cargo build --no-default-features --features std

# Ver features disponíveis
cargo metadata --no-deps | jq '.packages[0].features'
```

### Build profiles e flags

```toml
# Cargo.toml
[profile.release]
lto = "thin"          # link-time optimization
codegen-units = 1     # melhor otimização (mais lento p/ compilar)
opt-level = 3
panic = "abort"

[profile.dev]
opt-level = 0
```

```bash
# Cross-compile
cargo build --target x86_64-unknown-linux-musl

# Instalar target (rustup)
rustup target add aarch64-unknown-linux-gnu
rustup target list --installed

# Target por config (arquivo .cargo/config.toml)
```

## 🔍 Análise de Código

* `Clippy avançado`

```bash
# Todas as lints de pedância
cargo clippy -- -W clippy::pedantic

# Lints de performance e correção
cargo clippy -- -W clippy::perf -W clippy::correctness

# Fix automático
cargo clippy --fix
cargo clippy --fix --allow-dirty

# Silenciar uma lint em código
# #[allow(clippy::module_name_repetitions)]
```

* `Formatação com controle`

```bash
# Formatar com opções
cargo fmt -- --check
cargo fmt -- --config tab_spaces=4

# Ignorar arquivos (rustfmt.toml)
```

* `Auditoria de dependências`

```bash
# Vulnerabilidades (ferramenta de terceiros)
# cargo install cargo-audit
cargo audit

# Licenças (ferramenta de terceiros)
# cargo install cargo-license
cargo license

# Dependências de dev/regular
cargo tree -e normal
cargo tree -d   # duplicadas
```

## ⚡ Otimização de Performance

### Profiling

```bash
# Instalar ferramentas
cargo install flamegraph
cargo install cargo-criterion

# Flamegraph (Linux/macOS)
cargo flamegraph
cargo flamegraph --bin meu-binario

# Benchmarks (criterion)
cargo bench
cargo bench --bench meu_bench
```

```rust
// Em benches/bench.rs
use criterion::{criterion_group, criterion_main, Criterion};

fn bench(c: &mut Criterion) {
    c.bench_function("fibo 30", |b| b.iter(|| fib(30)));
}
criterion_group!(benches, bench);
criterion_main!(benches);
```

* `Estimar consumo de memória`

```bash
# Valgrind massif (Linux)
valgrind --tool=massif ./target/release/app
ms_print massif.out.* | head -50

# (macOS usa Instruments/DTrace)
```

## 🧵 Concorrência

* `Threads e canais`

```rust
use std::thread;
use std::sync::mpsc;

// Threads com move
let handle = thread::spawn(move || {
    // usa dados movidos
});

handle.join().unwrap();

// Canais
let (tx, rx) = mpsc::channel();
tx.send(42).unwrap();
let valor = rx.recv().unwrap(); // bloqueia
```

* `Arc + Mutex` (estado compartilhado)

```rust
use std::sync::{Arc, Mutex};

let contador = Arc::new(Mutex::new(0));
let contador2 = Arc::clone(&contador);

thread::spawn(move || {
    let mut n = contador2.lock().unwrap();
    *n += 1;
});

let n = *contador.lock().unwrap();
println!("{n}");
```

* `Async/await (tokio)`

```toml
# Cargo.toml
[dependencies]
tokio = { version = "1", features = ["full"] }
```

```rust
#[tokio::main]
async fn main() {
    let res = fetch_dados().await;
    println!("{res}");
}

async fn fetch_dados() -> String {
    // simula I/O assíncrono
    tokio::time::sleep(std::time::Duration::from_millis(10)).await;
    "dados".to_string()
}
```

* `RwLock e channels de alta performance`

```rust
use std::sync::RwLock;

// Muitos leitores, poucos escritores
let lock = RwLock::new(vec![1, 2, 3]);
{
    let leitura = lock.read().unwrap();
    println!("{:?}", *leitura);
}
let mut escrita = lock.write().unwrap();
escrita.push(4);
```

## 🧬 Traits e Genéricos

* `Traits com implementação por default`

```rust
trait Saudacao {
    fn nome(&self) -> &str;
    fn saudar(&self) -> String {
        format!("Olá, {}", self.nome())
    }
}

struct Usuario { nome: String }

impl Saudacao for Usuario {
    fn nome(&self) -> &str { &self.nome }
}

let u = Usuario { nome: "Ana".into() };
println!("{}", u.saudar());
```

* `Generics com trait bounds`

```rust
fn maior<T: PartialOrd>(a: T, b: T) -> T {
    if a > b { a } else { b }
}

// Multiplos bounds
fn processar<T: Clone + std::fmt::Debug>(item: T) {
    println!("{:?}", item.clone());
}
```

* `Result e Option (error handling idiomático)`

```rust
fn dividir(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        return Err("divisão por zero".into());
    }
    Ok(a / b)
}

// Propagação com ?
fn calculo() -> Result<f64, String> {
    let q = dividir(10.0, 2.0)?;   // ? propaga o erro
    Ok(q * 2.0)
}

// Options
fn primeiro(v: &[i32]) -> Option<i32> {
    v.first().copied()
}
```

## 🧩 Macros

* `Macros declarativas`

```rust
macro_rules! vetor {
    ($($x:expr),* $(,)?) => {
        vec![$($x),*]
    };
}

let v = vetor!(1, 2, 3);
```

* `Derive (derivadas)`

```rust
#[derive(Debug, Clone, PartialEq, Eq)]
struct Config {
    nome: String,
    porta: u16,
}
```

## 🧪 Testes Avançados

```rust
// Testes com casos de erro e ignore
#[test]
#[should_panic(expected = "division by zero")]
fn dividir_por_zero_panica() {
    let _ = 1.0 / 0.0;
}

#[test]
#[ignore = "lento - roda manualmente"]
fn teste_lento() {
    // ...
}

// Testes parametrizados (no crate test-case)
// #![feature] ou crate dev
```

```bash
# Rodar apenas testes ignorados
cargo test -- --ignored

# Filtrar por módulo
cargo test modulo::

# Testes com realease
cargo test --release
```

## 🔒 Unsafe

```rust
// Unsafe raramente necessário; isole e documente
unsafe {
    // deref de ponteiro bruto
    let ptr = &mut valor as *mut i32;
    *ptr += 1;
}

// Use as ferramentas de verificação
// cargo miri (interpretador) para detectar UB
```

```bash
# Miri - detector de comportamento indefinido (requer componente nightly)
rustup toolchain install nightly
rustup component add miri --toolchain nightly
cargo +nightly miri test
```

## 🛠️ Ferramentas do Ecossistema

```bash
# Formatação, lint, fix
cargo fmt --all
cargo clippy --fix
cargo fix

# Nextest (test runner mais rápido)
cargo install cargo-nextest
cargo nextest run

# Expandir macros (ver código gerado)
cargo install cargo-expand
cargo expand

# Hot reload
cargo install cargo-watch
cargo watch -x run

# Binários prontos (musl)
cargo build --release --target x86_64-unknown-linux-musl
```

## 📋 Checklist de Rust Avançado

| Tarefa | Comando/Ferramenta |
| ------ | ------------------ |
| Workspace | `cargo build --workspace` |
| Features | `cargo build --features` |
| Lint agressivo | `cargo clippy -- -W clippy::pedantic` |
| Perf | `cargo flamegraph` / `cargo bench` |
| UB checker | `cargo +nightly miri test` |
| Cross-compile | `cargo build --target` |
| Segurança de deps | `cargo audit` |
| Hot reload | `cargo watch -x run` |

## 📚 Referências

* The Rust Book (capítulos avançados)
* Rust Reference
* Cargo Book
* Rust Design Patterns

Pronto para dominar Rust em produção! 🦀🚀