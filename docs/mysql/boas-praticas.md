# 📘 Boas Práticas no MySQL

> Guia de boas práticas para operar MySQL de forma segura, performática e organizada

---

## 🔐 Segurança

### 1. Nunca exponha credenciais no código

```bash
# ❌ NUNCA FAÇA ISSO
mysql -u root -p123456 banco
# ou
echo "senha=123" > config.sql

# ✅ Use variáveis de ambiente ou arquivos de config protegidos
mysql -u usuario -p"$DB_PASS" banco
# ou use o arquivo de config protegido (senha fora do histórico)
mysql --defaults-file=~/.my.cnf banco
```

### 2. Senhas fortes e usuários com privilégio mínimo

```sql
-- ✅ Menor privilégio possível
CREATE USER 'app'@'%' IDENTIFIED BY 'S3nh@F0rte!';
GRANT SELECT, INSERT, UPDATE ON banco.* TO 'app'@'%';

-- ❌ Evitar usuário root para aplicações
-- ❌ Evitar GRANT ALL em produção

-- Revisar usuários existentes
SELECT User, Host FROM mysql.user;
```

### 3. Bloquear acesso externo desnecessário

```bash
# MySQL escuta apenas em localhost por padrão
# /etc/mysql/mysql.conf.d/mysqld.cnf:
# bind-address = 127.0.0.1

# Porta 3306 só para hosts internos
sudo ufw allow from 192.168.1.0/24 to any port 3306
```

## 🗄️ Modelagem de Dados

### 1. Sempre usar PRIMARY KEY

```sql
-- ✅ Todo tabela tem uma PK
CREATE TABLE usuarios (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    ...
);

-- ❌ Tabelas sem PK causam lentidão em replicação e UPDATE
```

### 2. Escolher tipos certos

```sql
-- ✅ Menor tipo suficiente
id INT UNSIGNED
idade TINYINT UNSIGNED
preco DECIMAL(10,2)          -- dinheiro: DECIMAL, não FLOAT
status ENUM('ativo','inativo')

-- ❌ VARCHAR(255) onde cabe VARCHAR(50)
-- ❌ DATETIME armazenando só datas (use DATE)
```

### 3. Normalizar até o terceiro nível (quando fizer sentido)

```sql
-- ✅ Dados repetidos ficam em tabelas relacionadas
usuarios / pedidos / itens_pedido

-- ❌ Colunas como "cidade_1", "cidade_2"
-- ❌ Armazenar listas separadas por vírgula
```

## 📊 Índices

### 1. Indexar colunas usadas em WHERE/JOIN/ORDER BY

```sql
CREATE INDEX idx_email ON usuarios(email);
CREATE INDEX idx_usuario_id ON pedidos(usuario_id);

-- Índice composto: coluna mais seletiva primeiro
CREATE INDEX idx_cidade_nome ON usuarios(cidade, nome);
```

### 2. Evitar índices desnecessários

```sql
-- ❌ Índices em colunas raramente usadas custam INSERT/UPDATE
DROP INDEX idx_raramente_usado ON usuarios;

-- Ver índices que não são usados
SELECT * FROM sys.schema_unused_indexes;
```

## 🔍 Consultas

### 1. Sempre usar WHERE em UPDATE/DELETE

```sql
-- ❌ Perigosíssimo
DELETE FROM usuarios;

-- ✅ Sempre com condição
DELETE FROM usuarios WHERE ultimo_acesso < '2023-01-01';

-- Para esvaziar de propósito
TRUNCATE TABLE usuarios;
```

### 2. Evitar SELECT *

```sql
-- ✅ Só colunas necessárias
SELECT nome, email FROM usuarios;

-- ❌ SELECT * traz colunas desnecessárias (mais I/O e rede)
```

### 3. Usar LIMIT quando aplicável

```sql
-- ✅ Limitar resultados de relatórios
SELECT * FROM logs ORDER BY data DESC LIMIT 100;
```

### 4. EXPLAIN antes de otimizar

```sql
EXPLAIN SELECT * FROM usuarios WHERE email = 'x@x.com';
-- Verificar se o tipo de acesso é 'ref'/'eq_ref' (usa índice)
-- ou 'ALL' (full scan - provável falta de índice)
```

## 🔁 Transações

```sql
-- ✅ Operações que dependem umas das outras: transação
START TRANSACTION;
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
UPDATE contas SET saldo = saldo + 100 WHERE id = 2;
COMMIT;  -- ou ROLLBACK em erro

-- ❌ Muitas autocommits isolados
```

## 🧰 Backup

### 1. Backup regular e testado

```bash
# Diário completo
mysqldump --single-transaction --routines banco > backup_$(date +%F).sql

# Comprimir
mysqldump --single-transaction banco | gzip > backup_$(date +%F).sql.gz

# ⚠️ SEMPRE testar a restauração em ambiente separado!
mysql -u root banco_teste < backup.sql
```

### 2. Reter versões

```bash
# Manter últimos 7 dias
find /backup -name "*.sql*" -mtime +7 -delete
```

## 📈 Monitoramento

```sql
-- Processos ativos
SHOW FULL PROCESSLIST;

-- Tabelas com mais linhas / espaço
SELECT table_schema, table_name, table_rows,
       ROUND(data_length/1024/1024,2) AS size_mb
FROM information_schema.tables
ORDER BY size_mb DESC LIMIT 10;

-- Variáveis importantes
SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
SHOW VARIABLES LIKE 'max_connections';
```

## 📋 Checklist de Boas Práticas MySQL

* 🔐 Senhas fortes, usuários com privilégio mínimo
* 🗄️ PK em toda tabela, tipos adequados
* 📊 Índices em colunas de WHERE/JOIN
* 🔍 UPDATE/DELETE sempre com WHERE
* ❌ Evitar SELECT *
* 🔁 Transações para operações multi-statement
* 🧰 Backup diário testado
* 📈 Monitorar processo e variáveis-chave
* 🌐 Porta 3306 restrita a hosts internos

## 🎯 Resumo

| Prática | Recomendação |
|-------- |------------- |
| Credenciais | Nunca no código; usar env/config |
| Privilégios | Mínimo necessário |
| Modelagem | PK em toda tabela, tipos certos |
| Índices | Em colunas de WHERE/JOIN |
| UPDATE/DELETE | Sempre com WHERE |
| SELECT | Apenas colunas necessárias |
| Transações | Para grupos de statements |
| Backup | Diário, comprimido e testado |

## 📚 Referências

* MySQL 8.0 Reference Manual
* MySQL Performance Tuning
* MySQL Security Best Practices
* Percona Blog (otimização)

Pronto para operar MySQL com boas práticas! 🐬🚀