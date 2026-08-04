# 📘 Comandos Básicos do Rust

> Guia essencial para começar a usar Rust e o gerenciador de pacotes Cargo no dia a dia

---

## 🔧 Instalação e Configuração

* `Instalar o Rust (rustup)`

```bash
# Instalar rustup (gerenciador de toolchains)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Recarregar o ambiente
source "$HOME/.cargo/env"
```

* `Verificar instalação`

```bash
# Versões
rustc --version
cargo --version
rustup --version

# Ver toolchain atual
rustup show

# Atualizar toolchain
rustup update
```

* `Gerenciar toolchains`

```bash
# Instalar toolchain específica
rustup toolchain install stable
rustup toolchain install 1.75.0

# Definir toolchain padrão
rustup default stable

# Definir toolchain por projeto (rust-toolchain.toml)
rustup override set nightly
```

## 🚀 Iniciando um Projeto

* `Criar projeto novo`

```bash
# Projeto binário (executável)
cargo new meu-projeto
cargo new --bin meu-projeto

# Projeto biblioteca
cargo new --lib minha-lib

# Criar em diretório atual
cargo init

# Projeto com versão do git
cargo new projeto --vcs git
```

* `Estrutura gerada`

```
meu-projeto/
├── Cargo.toml     # Dependências e configuração
├── .gitignore
└── src/
    └── main.rs    # Código-fonte
```

## 🏗️ Compilação e Execução

* `Compilar`

```bash
# Compilar em debug (padrão)
cargo build

# Compilar e executar
cargo run

# Compilar em modo release (otimizado)
cargo build --release
cargo run --release
```

* `Verificar sem compilar`

```bash
# Checar erros de tipos (rápido)
cargo check

# Compilar um arquivo simples (sem Cargo)
rustc arquivo.rs
./arquivo
```

## 📦 Gerenciamento de Dependências

* `Adicionar dependências`

```bash
# Adicionar crate ao Cargo.toml
cargo add serde
cargo add serde --features derive
cargo add rand --rename minha_rand

# Adicionar dependência de dev
cargo add --dev criterion
```

* `Gerenciar dependências`

```bash
# Baixar dependências
cargo fetch

# Ver árvore de dependências
cargo tree

# Ver dependências desatualizadas (ferramenta de terceiros)
# cargo install cargo-outdated
cargo outdated

# Atualizar dependências
cargo update
cargo update -p rand

# Remover dependência
cargo remove rand
```

## 🧪 Testes

* `Rodar testes`

```bash
# Rodar todos os testes
cargo test

# Rodar com filtro de nome
cargo test nome_do_teste

# Rodar apenas um teste
cargo test nome_exato -- --exact

# Rodar testes com saída
cargo test -- --nocapture

# Rodar testes em paralelo (padrão)
cargo test -- --test-threads=4
```

* `Testes de documentação`

```bash
# Rodar doctests (exemplos em ///)
cargo test --doc
```

## 🔍 Lint e Formatação

```bash
# Lint (o compilador já avisa, isso deixa estrito)
cargo clippy

# Clippy com todas as verificações
cargo clippy -- -W clippy::all

# Formatação do código
cargo fmt

# Verificar se está formatado
cargo fmt --check
```

## 📦 Crates e Publicação

* `Buscar e publicar`

```bash
# Buscar crates no registro
cargo search serde

# Ver informações do crate
cargo info serde

# Publicar (após preparar)
cargo publish
```

## 🛠️ Comandos Úteis

* `Documentação`

```bash
# Gerar documentação local
cargo doc

# Gerar e abrir no navegador
cargo doc --open

# Documentar apenas o crate atual (exclui dependências)
cargo doc --no-deps
```

* `Executar exemplos`

```bash
# Rodar exemplo (arquivos em examples/)
cargo run --example exemplo

# Rodar binário específico quando há múltiplos
cargo run --bin binario
```

* `Limpeza e info`

```bash
# Remover artefatos de build
cargo clean

# Ver manifest em forma final
cargo metadata

# Mostrar o Package ID/spec do crate
cargo pkgid
```

## 🆘 Ajuda

```bash
# Ajuda geral
cargo --help
rustc --help

# Ajuda de um comando
cargo build --help
cargo add --help
```

## 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `cargo new` | Criar projeto |
| `cargo build` | Compilar |
| `cargo run` | Executar |
| `cargo test` | Rodar testes |
| `cargo clippy` | Lint |
| `cargo fmt` | Formatar |
| `cargo add` | Adicionar dependência |
| `cargo doc` | Gerar docs |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Instalação** | `rustup`, `rustc`, `cargo` |
| **Projeto** | `cargo new`, `cargo init` |
| **Build** | `cargo build`, `cargo run`, `cargo check` |
| **Dependências** | `cargo add`, `cargo update`, `cargo tree` |
| **Testes** | `cargo test` |
| **Lint/Fmt** | `cargo clippy`, `cargo fmt` |
| **Docs** | `cargo doc`, `cargo doc --open` |
| **Ajuda** | `--help`, `rustc --help` |

## 📚 Referências

* The Rust Book (documentação oficial)
* Rust By Example
* crates.io

Pronto para programar Rust com confiança! 🦀🚀