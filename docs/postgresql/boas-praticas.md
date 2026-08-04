# 📘 Boas Práticas no PostgreSQL

> Guia de boas práticas para operar PostgreSQL de forma segura, performática e de fácil manutenção

---

## 🔐 Segurança

### 1. Nunca exponha credenciais

```bash
# ❌ NUNCA FAÇA ISSO
psql -U admin -d db -c "SELECT ... " --password=123456

# ✅ Use autenticação por arquivo de config ou variável
export PGPASSWORD="$DB_PASS"
psql -h host -U app -d banco

# ✅ Melhor: use .pgpass com permissão 600
chmod 600 ~/.pgpass
```

### 2. Usuários com privilégio mínimo

```sql
-- ✅ Menor privilégio
CREATE USER app WITH PASSWORD 'S3nh@';
GRANT CONNECT ON DATABASE banco TO app;
GRANT USAGE ON SCHEMA public TO app;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app;

-- ❌ Evitar superuser para aplicações
-- ❌ Não usar postgres (superuser) em conexões de app
```

### 3. Controle de acesso à rede

```bash
# pg_hba.conf — restringir por host
# host all app 192.168.1.0/24 md5
# host all app 0.0.0.0/0 reject

# Verificar pg_hba.conf
cat $PGDATA/pg_hba.conf
```

## 🗄️ Modelagem

### 1. Tipos adequados

```sql
-- ✅ Texto dinâmico grande: TEXT, curto: VARCHAR(n)
-- ✅ Dinheiro: NUMERIC/DECIMAL (não float)
-- ✅ Data+hora: TIMESTAMPTZ (não TIMESTAMP)
-- ✅ IDs: BIGSERIAL ou UUID (com extensão gen_random_uuid)
```

### 2. PK e NOT NULL sempre

```sql
CREATE TABLE usuarios (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    nome VARCHAR(200) NOT NULL,
    email CITEXT UNIQUE NOT NULL
);
```

### 3. Constraints e checks

```sql
CREATE TABLE pedidos (
    valor NUMERIC(10,2) CHECK (valor > 0),
    status TEXT CHECK (status IN ('pago', 'pendente', 'cancelado'))
);
```

## 📊 Índices

* `Indexe o que é consultado`

```sql
-- ✅ Para WHERE/JOIN
CREATE INDEX idx_usuario_id ON pedidos(usuario_id);

-- ✅ Para ordenação
CREATE INDEX idx_data ON pedidos(criado_em DESC);

-- ✅ Índice composto (ordem importa)
CREATE INDEX idx_cidade_nome ON usuarios(cidade, nome);
```

* `Evite índices redundantes`

```sql
-- ❌ Índice em coluna pouco consultada custa escrita
-- ❌ Índices duplicados

-- Ver índices não usados
SELECT schemaname, relname, indexrelname
FROM pg_stat_user_indexes
WHERE idx_scan = 0;
```

## 🔍 Consultas

* **Sempre use WHERE em UPDATE/DELETE**

```sql
-- ❌ Esvazia acidental
DELETE FROM usuarios;

-- ✅
DELETE FROM usuarios WHERE status = 'cancelado';

-- Para esvaziar de propósito
TRUNCATE usuarios;
```

* **Evite SELECT ***

```sql
-- ✅ Colunas necessárias
SELECT nome, email FROM usuarios;
```

* **EXPLAIN antes de otimizar**

```sql
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;
-- procure Seq Scan em tabelas grandes (falta de índice)
```

## 🔁 Transações

```sql
-- ✅ Group de statements que dependem uns dos outros
BEGIN;
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
UPDATE contas SET saldo = saldo + 100 WHERE id = 2;
COMMIT;  -- ou ROLLBACK
```

## 🧯 Backup

* **Backup diário e testado**

```bash
pg_dump -Fc banco > /backup/banco_$(date +%F).dump
# SEMPRE testar a restauração em ambiente separado
pg_restore -d banco_teste /backup/banco_<data>.dump
```

* **Reter versões**

```bash
find /backup -name "*.dump" -mtime +7 -delete
```

## 📈 Manutenção

```sql
-- ✅ VACUUM e ANALYZE regulares (autovacuum normalmente cuida)
VACUUM;
ANALYZE;

-- Ver autovacuum ativo
SHOW autovacuum;
SHOW autovacuum_vacuum_scale_factor;
```

## 📋 Checklist de Boas Práticas PostgreSQL

* 🔐 Sem senha em código; privilégio mínimo
* 🗄️ UUID ou SERIAL como PK, NOT NULL, constraints
* 📊 Índices em colunas de WHERE/JOIN/ORDER
* 🔍 UPDATE/DELETE sempre com WHERE
* ❌ Evitar SELECT *
* 🔁 Transações para multi-statement
* 🎯 Backup diário testado
* 🔁 Autovacuum ativo e monitorado
* 🌐 Acesso de rede restrito (pg_hba.conf)

## 🎯 Resumo

| Prática | Recomendação |
| ------- | ------------ |
| Credenciais | .pgpass / variáveis de ambiente |
| Privilégios | Mínimo por usuário |
| PK | UUID ou SERIAL |
| Tipos | TIMESTAMPTZ, NUMERIC, JSONB |
| Índices | Em colunas consultadas |
| UPDATE/Delete | Sempre WHERE |
| Backup | Diário, custom (-Fc), testado |
| Manutenção | Autovacuum monitorado |

## 📚 Referências

* PostgreSQL Manual
* PostgreSQL "Practical Guide to ...
* Postgres Weekly

Pronto para operar PostgreSQL com boas práticas! 🐘🚀