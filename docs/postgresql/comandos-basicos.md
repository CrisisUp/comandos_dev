# 📘 Comandos Básicos do PostgreSQL

> Guia essencial para começar a usar PostgreSQL e o client psql no dia a dia

---

## 🔧 Conexão e Configuração

* `Instalar e iniciar`

```bash
# macOS (Homebrew)
brew install postgresql@16
brew services start postgresql@16

# Ubuntu/Debian
sudo apt install postgresql postgresql-contrib
sudo systemctl start postgresql
```

* `Conectar ao banco`

```bash
# Conectar como usuário do sistema
psql

# Conectar como usuário específico
psql -U usuario -d banco
psql -h localhost -p 5432 -U usuario -d banco

# Conectar com usuário postgres (Linux)
sudo -u postgres psql
```

* `Gerenciar serviços`

```bash
# Status e restart
sudo systemctl status postgresql
sudo systemctl restart postgresql

# macOS via brew
brew services list | grep postgres
```

## 🚀 Gerenciando Bancos de Dados

* `Criar e listar bancos`

```sql
-- Listar bancos
\l

-- Criar banco
CREATE DATABASE nome_banco;

-- Criar com encoding/collation
CREATE DATABASE nome_banco
  ENCODING 'UTF8'
  LC_COLLATE 'pt_BR.UTF-8'
  LC_CTYPE 'pt_BR.UTF-8';

-- Selecionar/remover
\c nome_banco
DROP DATABASE nome_banco;
```

* `Criar usuário e permissão`

```sql
-- Criar usuário
CREATE USER usuario WITH PASSWORD 'senha';

-- Criar usuário com privilégios de superusuario
CREATE USER admin WITH SUPERUSER PASSWORD 'senha';

-- Conceder/revogar
GRANT ALL PRIVILEGES ON DATABASE nome_banco TO usuario;
REVOKE ALL ON DATABASE nome_banco FROM usuario;

-- Alterar senha
ALTER USER usuario WITH PASSWORD 'nova_senha';

-- Remover
DROP USER usuario;
```

## 🗄️ Tabelas e Estrutura

* `Criar tabelas`

```sql
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    idade INT CHECK (idade >= 0),
    criado_em TIMESTAMPTZ DEFAULT NOW()
);
```

* `Ver e alterar estrutura`

```sql
-- Listar tabelas
\dt
\dt+                    -- com detalhes

-- Ver definição
\d nome_tabela
\d+ nome_tabela

-- Alterar tabela
ALTER TABLE usuarios ADD COLUMN telefone VARCHAR(15);
ALTER TABLE usuarios DROP COLUMN telefone;
ALTER TABLE usuarios RENAME TO clientes;

-- Index
CREATE INDEX idx_email ON usuarios(email);
```

## 🔍 Consultas (SELECT)

```sql
-- Selecionar
SELECT * FROM usuarios;
SELECT nome, email FROM usuarios WHERE idade > 18;

-- Ordenação/limite
SELECT * FROM usuarios ORDER BY criado_em DESC LIMIT 10;

-- Agrupamento
SELECT cidade, COUNT(*) AS total
FROM usuarios
GROUP BY cidade
HAVING COUNT(*) > 5;

-- Full text search
SELECT * FROM posts
WHERE to_tsvector('portuguese', titulo) @@ to_tsquery('banco & dados');
```

## ✍️ Inserindo, Atualizando e Deletando

```sql
-- Insert
INSERT INTO usuarios (nome, email, idade) VALUES ('Ada', 'ada@x.com', 36);

-- Insert em massa
INSERT INTO usuarios (nome, email) VALUES
  ('A', 'a@x.com'),
  ('B', 'b@x.com'),
  ('C', 'c@x.com');

-- Update
UPDATE usuarios SET idade = 37 WHERE email = 'ada@x.com';

-- Delete
DELETE FROM usuarios WHERE idade IS NULL;

-- Truncate (esvazia mas mantém estrutura)
TRUNCATE TABLE usuarios;
```

## 🔗 Relacionamentos e Joins

```sql
-- INNER JOIN
SELECT u.nome, p.valor
FROM usuarios u
JOIN pedidos p ON u.id = p.usuario_id;

-- LEFT JOIN
SELECT u.nome, COUNT(p.id) AS pedidos
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
GROUP BY u.id;

-- FULL OUTER JOIN (PostgreSQL suporta nativamente!)
SELECT u.nome, p.id
FROM usuarios u
FULL OUTER JOIN pedidos p ON u.id = p.usuario_id;
```

## 🛠️ Comandos psql Úteis

* `Meta-comandos`

```sql
-- Histórico e ajuda
\?      -- ajuda dos meta-comandos
\h SELECT --help de SQL

-- Psql control
\set ECHO_HIDDEN on

-- Descrever/saída
\pset pager off
\o saida.txt        -- redireciona saída
\o --              -- volta ao terminal

-- Arquivar e sair
\q
```

* `PSQL SQL and batch`

```bash
# Rodar script SQL
psql -U usuario -d banco -f script.sql

# Rodar comando único
psql -U usuario -d banco -c "SELECT * FROM usuarios;"

# Rodar vários
psql -U usuario -d banco -f - <<'EOF'
SELECT 1;
SELECT 2;
EOF
```

## ⬇️ Exportar e Importar

* `Exportar`

```bash
# Dump de um banco
pg_dump banco > backup.sql
pg_dump -U usuario banco > backup.sql

# Dump com compressão
pg_dump banco | gzip > backup.sql.gz

# Dump apenas estrutura ou dados
pg_dump --schema-only banco > estrutura.sql
pg_dump --data-only banco > dados.sql

# Dump em formato custom (restauração flexível)
pg_dump -Fc banco > backup.dump
```

* `Importar`

```bash
# Restaurar SQL
psql banco < backup.sql
psql -U usuario banco < backup.sql

# Restaurar formato custom
pg_restore -d banco backup.dump

# Importar CSV
\copy usuarios FROM 'arquivo.csv' WITH (FORMAT csv, HEADER true);
```

## 📊 Monitoring/Limpeza

```sql
-- Bancos e tamanhos
\l+
SELECT pg_size_pretty(pg_database_size('banco'));

-- Conexões ativas
SELECT count(*) FROM pg_stat_activity;
SELECT pg_terminate_backend(pid)
FROM pg_stat_activity
WHERE datname = 'banco_antigo';

-- Vacuum básico
VACUUM;
ANALYZE;
```

## 📋 Checklist Diário

| Comando | O que faz |
| ------- | --------- |
| `psql -U usuario banco` | Conectar |
| `\l` | Listar bancos |
| `\dt` | Listar tabelas |
| `\d tabela` | Ver estrutura |
| `pg_dump banco` | Backup |
| `VACUUM; ANALYZE;` | Manutenção |
| `\q` | Sair |

## 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Conexão** | `psql`, `\l`, `\c` |
| **Bancos** | `CREATE DATABASE`, `pg_dump` |
| **Tabelas** | `CREATE TABLE`, `\d` |
| **Dados** | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **Joins** | `INNER`/`LEFT`/`FULL` |
| **Usuários** | `CREATE/ALTER/DROP USER`, `GRANT` |
| **Import/Export** | `pg_dump`, `psql <`, `\copy` |

## 📚 Referências

* Documentação Oficial do PostgreSQL
* PostgreSQL Tutorial
* psql Reference

Pronto para usar PostgreSQL com confiança! 🐘🚀