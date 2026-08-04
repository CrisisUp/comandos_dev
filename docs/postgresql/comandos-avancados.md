# 📘 Comandos Avançados do PostgreSQL

> Guia para usuários que já dominam o básico e querem performance, transações, replicação, particionamento e administração

---

## 🚀 Performance e Índices

* `Tipos de índices`

```sql
-- B-tree (padrão) - igualdade e range
CREATE INDEX idx_nome ON usuarios(nome);

-- GIN - arrays, JSONB, full text
CREATE INDEX idx_tags ON posts USING GIN (tags);
CREATE INDEX idx_dados ON produtos USING GIN (dados jsonb_path_ops);

-- Hash - apenas igualdade
CREATE INDEX idx_id ON contas USING HASH (id);

-- BRIN - dados ordenados e grandes
CREATE INDEX idx_tempo ON logs USING BRIN (criado_em);

-- Índice parcial
CREATE INDEX idx_ativos ON usuarios (email) WHERE ativo = true;
```

### EXPLAIN e plano de execução

```sql
-- Ver plano de execução
EXPLAIN SELECT * FROM usuarios WHERE email = 'a@x.com';
EXPLAIN ANALYZE SELECT * FROM usuarios WHERE email = 'a@x.com';

-- Formato JSON (para ferramentas)
EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON) SELECT * FROM usuarios;

-- Vetoriza (uma análise mais cara, executa)
EXPLAIN (ANALYZE, VERBOSE) SELECT ...;
```

## 🔁 Transações e Locking

```sql
-- Transação
BEGIN;
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
UPDATE contas SET saldo = saldo + 100 WHERE id = 2;
COMMIT;   -- ou ROLLBACK;

-- Savepoint
BEGIN;
UPDATE usuarios SET nome = 'A' WHERE id = 1;
SAVEPOINT sp1;
UPDATE usuarios SET nome = 'B' WHERE id = 1;
ROLLBACK TO SAVEPOINT sp1;
COMMIT;

-- Níveis de isolamento
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

* `Advisory locks e row locking`

```sql
-- Lock de linha
SELECT * FROM contas WHERE id = 1 FOR UPDATE;
SELECT * FROM contas WHERE id = 1 FOR SHARE;

-- Lidar com locks
-- Advisory locks (certa de sessão)
SELECT pg_advisory_lock(1234);
SELECT pg_advisory_unlock(1234);
```

## 📦 Advanced Types e Geração

* `JSONB`

```sql
-- Coluna JSONB
ALTER TABLE produtos ADD COLUMN dados JSONB;

-- Consultar JSON
SELECT dados->>'nome' FROM produtos;
SELECT * FROM produtos WHERE dados @> '{"cor": "azul"}';

-- Índice GIN em JSONB
CREATE INDEX ON produtos USING GIN (dados jsonb_path_ops);
```

* `Array`

```sql
-- Coluna array
CREATE TABLE turmas (alunos TEXT[]);

-- Consultar
SELECT * FROM turmas WHERE 'Ana' = ANY(alunos);
SELECT * FROM turmas WHERE alunos @> ARRAY['Ana'];
```

* `UUID`

```sql
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

CREATE TABLE contas (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    ...
);
```

## ⚙️ Funções e Stored Procedures

* `PL/pgSQL function`

```sql
CREATE OR REPLACE FUNCTION total_pedidos(p_usuario_id INT)
RETURNS INT AS $$
DECLARE
    total INT;
BEGIN
    SELECT COUNT(*) INTO total FROM pedidos WHERE usuario_id = p_usuario_id;
    RETURN total;
END;
$$ LANGUAGE plpgsql;

SELECT total_pedidos(1);
```

* `Trigger`

```sql
CREATE TABLE auditoria (
    id SERIAL PRIMARY KEY,
    tabela TEXT,
    acao TEXT,
    quando TIMESTAMPTZ DEFAULT NOW()
);

CREATE OR REPLACE FUNCTION auditar()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO auditoria (tabela, acao)
    VALUES (TG_TABLE_NAME, TG_OP);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_audit_usuarios
AFTER INSERT OR UPDATE OR DELETE ON usuarios
FOR EACH ROW EXECUTE FUNCTION auditar();
```

* `Views e materialized`

```sql
-- View
CREATE VIEW v_ativos AS SELECT * FROM usuarios WHERE ativo;

-- Materialized (cache, pode ser refeita)
CREATE MATERIALIZED VIEW mv_resumo AS
SELECT cidade, COUNT(*) FROM usuarios GROUP BY cidade;

REFRESH MATERIALIZED VIEW mv_resumo;
```

## 📄 Partitioning e Window Functions

* `Particionamento`

```sql
-- Tabela particionada por range
CREATE TABLE logs (
    id BIGSERIAL,
    criado_em TIMESTAMPTZ NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (criado_em);

-- Partições
CREATE TABLE logs_2024 PARTITION OF logs
  FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
CREATE TABLE logs_2025 PARTITION OF logs
  FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');
```

* `Window functions`

```sql
-- ROW_NUMBER (rank por grupo)
SELECT nome, cidade,
       ROW_NUMBER() OVER (PARTITION BY cidade ORDER BY nome) AS pos
FROM usuarios;

-- RANK / DENSE_RANK
SELECT nome, preco,
       RANK() OVER (ORDER BY preco DESC) AS rank
FROM produtos;

-- LAG/LEAD (linha anterior/seguinte)
SELECT id, valor,
       LAG(valor) OVER (ORDER BY id) AS anterior,
       LEAD(valor) OVER (ORDER BY id) AS seguinte
FROM vendas;

-- Running total
SELECT id, valor,
       SUM(valor) OVER (ORDER BY id) AS acumulado
FROM vendas;
```

## 🗂️ Herança, CTE (WITH) Recursivas

```sql
-- CTE simples
WITH recentes AS (
    SELECT * FROM pedidos WHERE criado_em > NOW() - INTERVAL '7 days'
)
SELECT * FROM recentes WHERE valor > 100;

-- CTE recursiva (arvore)
WITH RECURSIVE arvore AS (
    SELECT id, nome, parent_id FROM categorias WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.nome, c.parent_id
    FROM categorias c
    JOIN arvore a ON c.parent_id = a.id
)
SELECT * FROM arvore;
```

## ⬇️ Backup e Restauração Avançada

* `pg_dump avançado`

```bash
# Tudo (incluindo extensões)
pg_dump banco | gzip -9 > banco.sql.gz

# Formato custom com parallel restore
pg_dump -Fc banco -z -f banco.dump
pg_restore -d banco -j 4 banco.dump

# Sem transações (para dados sensíveis)
pg_dump --data-only --disable-triggers banco

# PITR com WAL dependente
pg_basebackup -D /backup -U replicador -P
```

* `psql restore`

```bash
pg_restore --clean --if-exists -d banco banco.dump
```

## 🔁 Replicação e Alta Disponibilidade

```sql
-- Configurar standby (arquivo postgresql.conf da réplica)
# hot_standby = on

-- No master
CREATE USER replicador WITH REPLICATION LOGIN PASSWORD 'senha';
SELECT pg_create_physical_replication_slot('slot1');

-- Promover standby a master
SELECT pg_promote();
```

## 🔍 Monitoramento

```bash
# Ver atividade/query
psql -c "SELECT pid, state, wait_event_type, query FROM pg_stat_activity;"

# Bloqueios
psql -c "SELECT * FROM pg_locks WHERE NOT granted;"

# Tamanho dos bancos/tabelas
psql -c "SELECT pg_size_pretty(pg_database_size('db')) AS tamanho;"
```

```sql
-- Cache hit ratio
SELECT
  sum(heap_blks_hit) / nullif(sum(heap_blks_hit) + sum(heap_blks_read), 0)
  AS hit_ratio
FROM pg_statio_user_tables;
```

## 📋 Checklist de PostgreSQL Avançado

| Tema | Comando/Função |
| ---- | -------------- |
| Índices | `CREATE INDEX ... USING GIN/GiST/BRIN` |
| Explicar | `EXPLAIN (ANALYZE, BUFFERS)` |
| JSONB | `dados @>`, índice GIN |
| Transações | `BEGIN ... COMMIT/ROLLBACK` |
| particionamento | `PARTITION BY RANGE` |
| Window | `ROW_NUMBER() OVER` |
| CTE recursivo | `WITH RECURSIVE` |
| Backup | `pg_dump -Fc` + `pg_restore -j` |

## 📚 Referências

* PostgreSQL Manual (explain, partitioning)
* PostgreSQL Performance Tuning
* pgsql-hackers

Pronto para dominar PostgreSQL em produção! 🐘🚀