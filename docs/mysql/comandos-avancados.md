# 📘 Comandos Avançados do MySQL

> Guia para usuários que já dominam o básico e querem otimização, procedures, transações, replicação e administração

---

## 🔍 Índices e Otimização

* `Criar e gerenciar índices`

```sql
-- Criar índice simples
CREATE INDEX idx_nome ON usuarios(nome);

-- Índice composto (ordem importa)
CREATE INDEX idx_cidade_nome ON usuarios(cidade, nome);

-- Índice único
CREATE UNIQUE INDEX idx_email ON usuarios(email);

-- Índice com prefixo (textos grandes)
CREATE INDEX idx_descricao ON produtos(descricao(10));

-- Ver índices de uma tabela
SHOW INDEX FROM usuarios;

-- Explicar plano de execução
EXPLAIN SELECT * FROM usuarios WHERE nome = 'João';
EXPLAIN FORMAT=JSON SELECT * FROM usuarios WHERE nome = 'João';
```

* `Query optimization`

```sql
-- ANÁLISE: atualizar estatísticas
ANALYZE TABLE usuarios;

-- Ver queries lentas
SELECT * FROM information_schema.processlist WHERE command = 'Query';
SHOW FULL PROCESSLIST;

-- FORCE/IGNORE index
SELECT * FROM usuarios FORCE INDEX (idx_email) WHERE email = 'x@x.com';
SELECT * FROM usuarios IGNORE INDEX (idx_nome) WHERE nome = 'João';

-- STRAIGHT_JOIN para controlar ordem
SELECT STRAIGHT_JOIN u.nome FROM usuarios u INNER JOIN pedidos p ON u.id = p.usuario_id;
```

* `Partitioning`

```sql
-- Particionar por faixa (range)
CREATE TABLE logs (
    id INT NOT NULL,
    created_at DATE NOT NULL
) PARTITION BY RANGE (YEAR(created_at)) (
    PARTITION p2022 VALUES LESS THAN (2023),
    PARTITION p2023 VALUES LESS THAN (2024),
    PARTITION p2024 VALUES LESS THAN (2025),
    PARTITION pmax VALUES LESS THAN MAXVALUE
);

-- Ver partições
SELECT PARTITION_NAME, TABLE_ROWS FROM information_schema.PARTITIONS WHERE TABLE_NAME = 'logs';
```

## 📦 Stored Procedures e Functions

* `Stored procedures`

```sql
-- Criar procedure
DELIMITER //
CREATE PROCEDURE BuscarUsuario(IN p_id INT)
BEGIN
    SELECT nome, email FROM usuarios WHERE id = p_id;
END//
DELIMITER ;

-- Chamar
CALL BuscarUsuario(1);

-- Procedure com OUT
DELIMITER //
CREATE PROCEDURE ContarUsuarios(OUT p_total INT)
BEGIN
    SELECT COUNT(*) INTO p_total FROM usuarios;
END//
DELIMITER ;
CALL ContarUsuarios(@total);
SELECT @total;

-- Remover
DROP PROCEDURE IF EXISTS BuscarUsuario;
```

* `Stored functions`

```sql
DELIMITER //
CREATE FUNCTION FormataData(d DATETIME) RETURNS VARCHAR(20)
DETERMINISTIC
BEGIN
    RETURN DATE_FORMAT(d, '%d/%m/%Y');
END//
DELIMITER ;

SELECT FormataData(NOW());

DROP FUNCTION IF EXISTS FormataData;
```

* `Triggers`

```sql
-- Trigger antes de insert
DELIMITER //
CREATE TRIGGER before_insert_usuario
BEFORE INSERT ON usuarios
FOR EACH ROW
BEGIN
    SET NEW.criado_em = NOW();
END//
DELIMITER ;

-- Trigger de auditoria
CREATE TABLE auditoria (
    id INT AUTO_INCREMENT PRIMARY KEY,
    tabela VARCHAR(50),
    acao VARCHAR(10),
    data DATETIME DEFAULT NOW()
);

DELIMITER //
CREATE TRIGGER after_delete_usuario
AFTER DELETE ON usuarios
FOR EACH ROW
BEGIN
    INSERT INTO auditoria (tabela, acao) VALUES ('usuarios', 'DELETE');
END//
DELIMITER ;

-- Ver triggers
SHOW TRIGGERS;
```

## 🔁 Transações

```sql
-- Iniciar transação explícita
START TRANSACTION;

INSERT INTO pedidos (usuario_id, valor) VALUES (1, 100);
UPDATE usuarios SET total_gasto = total_gasto + 100 WHERE id = 1;

-- Confirmar ou desfazer
COMMIT;
ROLLBACK;

-- Com savepoint
START TRANSACTION;
UPDATE usuarios SET nome = 'A' WHERE id = 1;
SAVEPOINT sp1;
UPDATE usuarios SET nome = 'B' WHERE id = 1;
ROLLBACK TO SAVEPOINT sp1;
COMMIT;

-- Níveis de isolamento
SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT @@transaction_isolation;
```

## 🔐 Usuários e Privilégios Avançados

```sql
-- Criar usuário com acesso de qualquer host
CREATE USER 'app'@'%' IDENTIFIED BY 'senha';

-- Privilégios granulares
GRANT SELECT, INSERT, UPDATE, DELETE ON banco.* TO 'app'@'%';
GRANT SELECT ON banco.relatorio TO 'leitor'@'%';
GRANT PROCESS, RELOAD ON *.* TO 'admin'@'localhost';
GRANT EXECUTE ON PROCEDURE banco.BuscarUsuario TO 'app'@'%';

-- Papéis (roles, MySQL 8+)
CREATE ROLE 'leitor_role';
GRANT SELECT ON banco.* TO 'leitor_role';
GRANT 'leitor_role' TO 'app'@'%';
SET DEFAULT ROLE 'leitor_role' TO 'app'@'%';

-- Ver privilégios
SHOW GRANTS FOR 'app'@'%';

-- Revoar e remover
REVOKE DELETE ON banco.* FROM 'app'@'%';
DROP USER 'app'@'%';

-- Auditoria de login
SELECT user, host, authentication_string, account_locked FROM mysql.user;
```

## 📊 Views e Materialized Views

```sql
-- Criar view
CREATE VIEW v_usuarios_ativos AS
SELECT id, nome, email FROM usuarios WHERE ativo = 1;

-- Ver views
SHOW FULL TABLES WHERE Table_type = 'VIEW';

-- View com agregação
CREATE VIEW v_resumo_pedidos AS
SELECT u.nome, COUNT(p.id) AS total_pedidos, SUM(p.valor) AS total
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
GROUP BY u.id;

-- Remover
DROP VIEW IF EXISTS v_usuarios_ativos;

-- MySQL não tem materialized views nativamente;
-- emular com tabela + refresh programado (event).
```

## 🛠️ Eventos Agendados

```sql
-- Ativar scheduler
SET GLOBAL event_scheduler = ON;

-- Criar evento que roda diariamente
CREATE EVENT diario_limpeza
ON SCHEDULE EVERY 1 DAY
STARTS TIMESTAMP(CURRENT_DATE, '03:00:00')
DO
  DELETE FROM logs WHERE data < DATE_SUB(NOW(), INTERVAL 30 DAY);

-- Criar evento único
CREATE EVENT backup_teste
ON SCHEDULE AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
DO
  UPDATE usuarios SET status = 'inativo' WHERE ativo = 0;

-- Ver e gerenciar eventos
SHOW EVENTS;
DROP EVENT IF EXISTS diario_limpeza;
```

## 🔁 Replicação

```bash
# No servidor principal (master) - my.cnf:
# [mysqld]
# server-id = 1
# log_bin = /var/log/mysql/mysql-bin.log

# No replica (slave) - my.cnf:
# [mysqld]
# server-id = 2
# relay-log = /var/lib/mysql/mysql-relay-bin
```

```sql
-- No master: criar usuário de replicação
CREATE USER 'repl'@'%' IDENTIFIED BY 'senha';
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'%';

-- Ver arquivo e posição binlog
SHOW MASTER STATUS;

-- Na replica: configurar origem
CHANGE MASTER TO
  MASTER_HOST='192.168.1.10',
  MASTER_USER='repl',
  MASTER_PASSWORD='senha',
  MASTER_LOG_FILE='mysql-bin.000001',
  MASTER_LOG_POS=154;

START SLAVE;
SHOW SLAVE STATUS\G;
```

## 📤 Backup e Restauração Avançada

```bash
# Backup com lock (consistente)
mysqldump --single-transaction --routines --triggers banco > backup.sql
mysqldump --single-transaction --all-databases > todos.sql

# Backup de structure only + data em arquivos separados
mysqldump --no-data --routines banco > estrutura.sql
mysqldump --no-create-info --single-transaction banco > dados.sql

# Backup compactado com checksum
mysqldump banco | gzip -9 > backup.sql.gz
sha256sum backup.sql.gz

# Restaurar com verificação de erros
mysql --force banco < backup.sql
gunzip -c backup.sql.gz | mysql banco

# PITR: logs binários para recuperação pontual
mysqlbinlog --start-datetime="2024-01-01 00:00:00" mysql-bin.000001 | mysql -u root
```

## 🧪 Testes e Diagnóstico

```sql
-- Verificar e reparar tabelas
CHECK TABLE usuarios;
CHECK TABLE usuarios QUICK;
REPAIR TABLE usuarios;

-- Ver motores de armazenamento
SELECT ENGINE, COUNT(*) FROM information_schema.TABLES GROUP BY ENGINE;

-- Otimizar após muitas alterações
OPTIMIZE TABLE usuarios;

-- Estatísticas do buffer pool
SHOW ENGINE INNODB STATUS\G;
```

## 📊 Performance Schema

```sql
-- Ver queries por tempo
SELECT DIGEST_TEXT, COUNT_STAR, SUM_ROWS_EXAMINED
FROM performance_schema.events_statements_summary_by_digest
ORDER BY COUNT_STAR DESC LIMIT 10;

-- Ver locks de metadados
SELECT * FROM performance_schema.metadata_locks;
```

## 📚 Referências

* MySQL 8.0 Reference Manual
* MySQL Performance Tuning
* Documentação de Replicação MySQL
* MySQL High Availability

Pronto para operar MySQL em produção! 🐬🚀
