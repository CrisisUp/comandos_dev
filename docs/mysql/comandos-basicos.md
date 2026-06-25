# 📘 Comandos Básicos do MySQL

> Guia essencial para começar a usar MySQL no dia a dia

---

## 🔌 Conexão e Gerenciamento

### Conectar ao MySQL

```bash
# Conectar como root (será solicitada a senha)
mysql -u root -p

# Conectar com usuário e senha especificados
mysql -u usuario -p

# Conectar a um host específico
mysql -h hostname -u usuario -p

# Conectar a uma porta específica
mysql -P 3306 -u usuario -p

# Conectar com arquivo de configuração
mysql --defaults-file=/caminho/.my.cnf

# Conectar e executar comando único
mysql -u root -p -e "SHOW DATABASES;"
```

* `Gerenciar usuários`

```sql
-- Ver usuários existentes
SELECT User, Host FROM mysql.user;

-- Criar novo usuário
CREATE USER 'usuario'@'localhost' IDENTIFIED BY 'senha';

-- Criar usuário com acesso de qualquer host
CREATE USER 'usuario'@'%' IDENTIFIED BY 'senha';

-- Alterar senha do usuário
ALTER USER 'usuario'@'localhost' IDENTIFIED BY 'nova_senha';

-- Conceder privilégios
GRANT ALL PRIVILEGES ON banco.* TO 'usuario'@'localhost';

-- Conceder privilégios específicos
GRANT SELECT, INSERT, UPDATE ON banco.* TO 'usuario'@'localhost';

-- Conceder privilégios para criar banco
GRANT CREATE, ALTER, DROP ON *.* TO 'usuario'@'localhost';

-- Remover privilégios
REVOKE ALL PRIVILEGES ON banco.* FROM 'usuario'@'localhost';

-- Remover usuário
DROP USER 'usuario'@'localhost';

-- Recarregar privilégios
FLUSH PRIVILEGES;
```

### 🗄️ Gerenciamento de Bancos de Dados

* `Criar e usar bancos`

```sql
-- Listar todos os bancos
SHOW DATABASES;

-- Criar banco de dados
CREATE DATABASE nome_banco;

-- Criar banco com charset específico
CREATE DATABASE nome_banco CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- Criar banco se não existir
CREATE DATABASE IF NOT EXISTS nome_banco;

-- Selecionar banco para usar
USE nome_banco;

-- Ver banco atual
SELECT DATABASE();

-- Remover banco (CUIDADO!)
DROP DATABASE nome_banco;

-- Remover banco se existir
DROP DATABASE IF EXISTS nome_banco;
Informações do banco
sql
-- Ver informações do banco
SELECT * FROM information_schema.SCHEMATA;

-- Ver tamanho do banco
SELECT table_schema "Banco",
       ROUND(SUM(data_length + index_length) / 1024 / 1024, 2) "Tamanho (MB)"
FROM information_schema.tables
GROUP BY table_schema;
```

### 📊 Gerenciamento de Tabelas

* `Criar tabelas`

```sql
-- Criar tabela básica
CREATE TABLE usuarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    idade INT,
    criado_em DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Criar tabela com chave estrangeira
CREATE TABLE pedidos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    usuario_id INT,
    produto VARCHAR(100),
    valor DECIMAL(10,2),
    FOREIGN KEY (usuario_id) REFERENCES usuarios(id)
);

-- Criar tabela com índices
CREATE TABLE produtos (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    preco DECIMAL(10,2) NOT NULL,
    categoria VARCHAR(50),
    INDEX idx_categoria (categoria),
    INDEX idx_nome (nome)
);

-- Criar tabela com restrições
CREATE TABLE funcionarios (
    id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    salario DECIMAL(10,2) CHECK (salario > 0),
    data_nascimento DATE,
    email VARCHAR(100) UNIQUE,
    ativo BOOLEAN DEFAULT TRUE
);

-- Criar tabela temporária
CREATE TEMPORARY TABLE temp_dados (
    id INT,
    nome VARCHAR(100)
);
```

* `Ver tabelas`

```sql
-- Listar todas as tabelas
SHOW TABLES;

-- Listar tabelas com padrão
SHOW TABLES LIKE 'user%';

-- Ver estrutura da tabela
DESCRIBE nome_tabela;
DESC nome_tabela;

-- Ver estrutura completa
SHOW CREATE TABLE nome_tabela;

-- Ver colunas
SHOW COLUMNS FROM nome_tabela;

-- Ver índices
SHOW INDEX FROM nome_tabela;
```

* `Alterar tabelas`

```sql
-- Adicionar coluna
ALTER TABLE usuarios ADD COLUMN telefone VARCHAR(15);

-- Adicionar coluna em posição específica
ALTER TABLE usuarios ADD COLUMN sobrenome VARCHAR(100) AFTER nome;

-- Adicionar coluna com DEFAULT
ALTER TABLE usuarios ADD COLUMN status VARCHAR(20) DEFAULT 'ativo';

-- Modificar coluna (tipo)
ALTER TABLE usuarios MODIFY COLUMN telefone VARCHAR(20);

-- Renomear coluna
ALTER TABLE usuarios CHANGE COLUMN telefone celular VARCHAR(20);

-- Remover coluna
ALTER TABLE usuarios DROP COLUMN celular;

-- Renomear tabela
RENAME TABLE usuarios TO clientes;

-- Adicionar índice
ALTER TABLE usuarios ADD INDEX idx_nome (nome);

-- Adicionar índice único
ALTER TABLE usuarios ADD UNIQUE INDEX idx_email (email);

-- Remover índice
ALTER TABLE usuarios DROP INDEX idx_nome;

-- Adicionar chave estrangeira
ALTER TABLE pedidos 
ADD CONSTRAINT fk_usuario 
FOREIGN KEY (usuario_id) REFERENCES usuarios(id);
```

* `Remover tabelas`

```sql
-- Remover tabela (CUIDADO!)
DROP TABLE nome_tabela;

-- Remover tabela se existir
DROP TABLE IF EXISTS nome_tabela;

-- Remover várias tabelas
DROP TABLE tabela1, tabela2, tabela3;

-- Limpar dados mas manter estrutura
TRUNCATE TABLE nome_tabela;
```

### 🔍 Consultas (SELECT)

* `Seleção básica`

```sql
-- Selecionar todos os dados
SELECT * FROM usuarios;

-- Selecionar colunas específicas
SELECT nome, email FROM usuarios;

-- Selecionar com alias
SELECT nome AS Nome, email AS Email FROM usuarios;

-- Selecionar dados únicos
SELECT DISTINCT cidade FROM usuarios;

-- Selecionar com limite
SELECT * FROM usuarios LIMIT 10;

-- Selecionar com offset
SELECT * FROM usuarios LIMIT 10 OFFSET 20;
SELECT * FROM usuarios LIMIT 20, 10;
```

* `Filtros (WHERE)`

```sql
-- Igualdade
SELECT * FROM usuarios WHERE nome = 'João';

-- Diferente
SELECT * FROM usuarios WHERE nome != 'João';

-- Maior/menor
SELECT * FROM usuarios WHERE idade > 18;
SELECT * FROM usuarios WHERE idade >= 18;
SELECT * FROM usuarios WHERE preco < 100;

-- Entre
SELECT * FROM produtos WHERE preco BETWEEN 10 AND 50;

-- LIKE (busca parcial)
SELECT * FROM usuarios WHERE nome LIKE 'Jo%';     -- Começa com Jo
SELECT * FROM usuarios WHERE nome LIKE '%Silva%'; -- Contém Silva
SELECT * FROM usuarios WHERE nome LIKE '_oão';     -- Segunda letra o

-- IN (múltiplos valores)
SELECT * FROM usuarios WHERE cidade IN ('São Paulo', 'Rio', 'Belo Horizonte');

-- IS NULL / IS NOT NULL
SELECT * FROM usuarios WHERE telefone IS NULL;
SELECT * FROM usuarios WHERE email IS NOT NULL;

-- Combinações (AND, OR, NOT)
SELECT * FROM usuarios 
WHERE idade > 18 
  AND cidade = 'São Paulo' 
  AND NOT status = 'inativo';
```

* `Ordenação (ORDER BY)`

```sql
-- Ordenar ascendente (padrão)
SELECT * FROM usuarios ORDER BY nome;

-- Ordenar descendente
SELECT * FROM usuarios ORDER BY nome DESC;

-- Ordenar múltiplas colunas
SELECT * FROM usuarios ORDER BY cidade ASC, nome DESC;

-- Ordenar com NULLs
SELECT * FROM usuarios ORDER BY telefone IS NULL, telefone;
```

* `Agrupamento (GROUP BY)`

```sql
-- Agrupar por cidade
SELECT cidade, COUNT(*) as total 
FROM usuarios 
GROUP BY cidade;

-- Agrupar com HAVING (filtro após agrupamento)
SELECT cidade, COUNT(*) as total 
FROM usuarios 
GROUP BY cidade 
HAVING total > 5;

-- Agrupar por múltiplas colunas
SELECT cidade, status, COUNT(*) 
FROM usuarios 
GROUP BY cidade, status;
Funções de agregação
sql
-- Contar registros
SELECT COUNT(*) FROM usuarios;
SELECT COUNT(id) FROM usuarios;
SELECT COUNT(DISTINCT cidade) FROM usuarios;

-- Soma
SELECT SUM(salario) FROM funcionarios;

-- Média
SELECT AVG(idade) FROM usuarios;

-- Mínimo/Máximo
SELECT MIN(preco), MAX(preco) FROM produtos;

-- Média com condição
SELECT AVG(CASE WHEN ativo = 1 THEN salario END) FROM funcionarios;
```

### ✏️ Inserção (INSERT)

* `Inserir dados`

```sql
-- Inserir com todas as colunas
INSERT INTO usuarios VALUES (1, 'João Silva', 'joao@email.com', 25, NOW());

-- Inserir com colunas específicas
INSERT INTO usuarios (nome, email, idade) 
VALUES ('Maria Santos', 'maria@email.com', 30);

-- Inserir múltiplos registros
INSERT INTO usuarios (nome, email, idade) VALUES
    ('Pedro Souza', 'pedro@email.com', 28),
    ('Ana Costa', 'ana@email.com', 22),
    ('Carlos Lima', 'carlos@email.com', 35);

-- Inserir com ON DUPLICATE KEY UPDATE
INSERT INTO usuarios (id, nome, email) 
VALUES (1, 'João Souza', 'joao@email.com')
ON DUPLICATE KEY UPDATE 
    nome = VALUES(nome),
    email = VALUES(email);

-- Inserir com IGNORE (ignora erros)
INSERT IGNORE INTO usuarios (id, nome) VALUES (1, 'Teste');
```

* `Inserir com SELECT`

```sql
-- Inserir resultado de SELECT
INSERT INTO usuarios_backup (nome, email, idade)
SELECT nome, email, idade FROM usuarios WHERE ativo = 1;

-- Inserir com JOIN
INSERT INTO relatorio (nome, total_pedidos)
SELECT u.nome, COUNT(p.id) 
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
GROUP BY u.id;
```

### 📝 Atualização (UPDATE)

Atualizar dados

```sql
-- Atualizar com condição
UPDATE usuarios SET idade = 26 WHERE nome = 'João Silva';

-- Atualizar múltiplas colunas
UPDATE usuarios 
SET nome = 'João Souza', email = 'joao.souza@email.com' 
WHERE id = 1;

-- Atualizar todos os registros (CUIDADO!)
UPDATE usuarios SET status = 'ativo';

-- Atualizar com cálculo
UPDATE produtos SET preco = preco * 1.10 WHERE categoria = 'eletronicos';

-- Atualizar com JOIN
UPDATE usuarios u
JOIN pedidos p ON u.id = p.usuario_id
SET u.total_gasto = u.total_gasto + p.valor
WHERE p.status = 'confirmado';

-- Atualizar com ORDER BY e LIMIT
UPDATE usuarios SET status = 'inativo' 
WHERE ultimo_acesso < '2023-01-01' 
ORDER BY id DESC 
LIMIT 10;

-- Atualizar múltiplos registros com CASE
UPDATE produtos 
SET preco = CASE 
    WHEN categoria = 'eletronicos' THEN preco * 1.10
    WHEN categoria = 'livros' THEN preco * 1.05
    ELSE preco * 1.02
END;
```

### 🗑️ Deleção (DELETE)

```sql
-- Deletar com condição
DELETE FROM usuarios WHERE id = 1;

-- Deletar múltiplos registros
DELETE FROM usuarios WHERE status = 'inativo' AND ultimo_acesso < '2023-01-01';

-- Deletar com JOIN
DELETE u FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
WHERE p.id IS NULL AND u.criado_em < '2022-01-01';

-- Deletar com ORDER BY e LIMIT
DELETE FROM logs WHERE data < '2023-01-01' ORDER BY data LIMIT 1000;

-- Deletar todos os registros (CUIDADO!)
DELETE FROM nome_tabela;

-- Deletar e resetar AUTO_INCREMENT
TRUNCATE TABLE nome_tabela;
```

### 🔗 Joins (Relacionamentos)

* `INNER JOIN`

```sql
-- Inner Join básico
SELECT u.nome, p.produto, p.valor
FROM usuarios u
INNER JOIN pedidos p ON u.id = p.usuario_id;

-- Inner Join com alias
SELECT u.nome AS Cliente, p.produto, p.valor
FROM usuarios u
JOIN pedidos p ON u.id = p.usuario_id;
LEFT JOIN
sql
-- Left Join (todos os usuários, com ou sem pedidos)
SELECT u.nome, COUNT(p.id) AS total_pedidos
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
GROUP BY u.id;

-- Left Join com condição
SELECT u.nome, p.produto
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id AND p.status = 'confirmado';
RIGHT JOIN
sql
-- Right Join (todos os pedidos, mesmo sem usuário)
SELECT u.nome, p.produto
FROM usuarios u
RIGHT JOIN pedidos p ON u.id = p.usuario_id;
FULL OUTER JOIN (MySQL não tem nativamente)
sql
-- Simular FULL OUTER JOIN
SELECT u.nome, p.produto
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
UNION
SELECT u.nome, p.produto
FROM usuarios u
RIGHT JOIN pedidos p ON u.id = p.usuario_id;
SELF JOIN
sql
-- Relacionamento hierárquico (funcionários com gerentes)
SELECT f.nome AS Funcionario, g.nome AS Gerente
FROM funcionarios f
LEFT JOIN funcionarios g ON f.gerente_id = g.id;
```

### 📊 Subconsultas

```sql
-- Subconsulta no WHERE
SELECT * FROM usuarios 
WHERE idade > (SELECT AVG(idade) FROM usuarios);

-- Subconsulta com IN
SELECT * FROM usuarios 
WHERE id IN (SELECT usuario_id FROM pedidos WHERE valor > 100);

-- Subconsulta com EXISTS
SELECT * FROM usuarios u
WHERE EXISTS (SELECT 1 FROM pedidos p WHERE p.usuario_id = u.id);

-- Subconsulta no SELECT
SELECT nome, 
       (SELECT COUNT(*) FROM pedidos WHERE usuario_id = u.id) AS total_pedidos
FROM usuarios u;

-- Subconsulta no FROM
SELECT AVG(total) 
FROM (SELECT usuario_id, COUNT(*) AS total 
      FROM pedidos 
      GROUP BY usuario_id) AS sub;
```

### 🛠️ Funções Úteis

* `Texto`

```sql
-- Concatenação
SELECT CONCAT(nome, ' - ', email) FROM usuarios;
SELECT CONCAT_WS(' ', nome, sobrenome) FROM usuarios;

-- Comprimento
SELECT LENGTH(nome) FROM usuarios;

-- Maiúsculas/Minúsculas
SELECT UPPER(nome), LOWER(email) FROM usuarios;

-- Substituição
SELECT REPLACE(nome, 'a', '@') FROM usuarios;

-- Substring
SELECT SUBSTRING(nome, 1, 3) FROM usuarios;

-- Remover espaços
SELECT TRIM(nome) FROM usuarios;

-- Extrair parte
SELECT LEFT(nome, 3), RIGHT(nome, 3) FROM usuarios;
Data/Hora
sql
-- Data atual
SELECT CURDATE();
SELECT CURRENT_DATE();

-- Hora atual
SELECT CURTIME();
SELECT CURRENT_TIME();

-- Data e hora atuais
SELECT NOW();
SELECT CURRENT_TIMESTAMP();

-- Extrair partes
SELECT YEAR(data_criacao) FROM tabela;
SELECT MONTH(data_criacao) FROM tabela;
SELECT DAY(data_criacao) FROM tabela;
SELECT HOUR(data_criacao) FROM tabela;

-- Formatação
SELECT DATE_FORMAT(data_criacao, '%d/%m/%Y') FROM tabela;

-- Adicionar/Subtrair
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY);
SELECT DATE_SUB(NOW(), INTERVAL 1 MONTH);

-- Diferença
SELECT DATEDIFF(NOW(), data_criacao) FROM tabela;
```

* `Numéricas`

```sql
-- Arredondamento
SELECT ROUND(preco, 2) FROM produtos;
SELECT FLOOR(preco) FROM produtos;
SELECT CEIL(preco) FROM produtos;

-- Absoluto
SELECT ABS(saldo) FROM contas;

-- Aleatório
SELECT RAND();
SELECT * FROM produtos ORDER BY RAND() LIMIT 1;
```

* `Condicionais`

```sql
-- CASE
SELECT nome,
       CASE 
           WHEN idade < 18 THEN 'Menor'
           WHEN idade >= 18 AND idade < 60 THEN 'Adulto'
           ELSE 'Idoso'
       END AS faixa_etaria
FROM usuarios;

-- IF
SELECT nome, IF(ativo = 1, 'Ativo', 'Inativo') AS status
FROM usuarios;

-- IFNULL
SELECT nome, IFNULL(telefone, 'Não informado') FROM usuarios;

-- COALESCE (primeiro não nulo)
SELECT COALESCE(telefone, celular, 'Sem contato') FROM usuarios;
```

### 📤 Exportar e Importar

* `Exportar dados`

```bash
# Exportar banco inteiro
mysqldump -u usuario -p nome_banco > backup.sql

# Exportar banco inteiro (com dados)
mysqldump -u usuario -p --complete-insert nome_banco > backup.sql

# Exportar apenas estrutura
mysqldump -u usuario -p --no-data nome_banco > estrutura.sql

# Exportar apenas dados
mysqldump -u usuario -p --no-create-info nome_banco > dados.sql

# Exportar tabelas específicas
mysqldump -u usuario -p nome_banco tabela1 tabela2 > backup.sql

# Exportar com compressão
mysqldump -u usuario -p nome_banco | gzip > backup.sql.gz

# Exportar para CSV
mysql -u usuario -p -e "SELECT * FROM usuarios" --batch --raw > usuarios.csv
```

* `Importar dados`

```bash
# Importar SQL
mysql -u usuario -p nome_banco < backup.sql

# Importar SQL com host específico
mysql -h hostname -u usuario -p nome_banco < backup.sql

# Importar SQL com banco diferente
mysql -u usuario -p -D outro_banco < backup.sql

# Importar de arquivo comprimido
gunzip -c backup.sql.gz | mysql -u usuario -p nome_banco
```

## Importar CSV (via mysql)

mysql -u usuario -p -e "LOAD DATA INFILE '/caminho/arquivo.csv' INTO TABLE nome_tabela FIELDS TERMINATED BY ',' ENCLOSED BY '\"' LINES TERMINATED BY '\n' IGNORE 1 ROWS;"

## Importar CSV (via linha de comando)

mysqlimport -u usuario -p --fields-terminated-by=',' nome_banco arquivo.csv

* `Importar via SQL`

```sql
-- Importar arquivo CSV
LOAD DATA INFILE '/caminho/arquivo.csv'
INTO TABLE usuarios
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS
(nome, email, idade);

-- Importar com substituição
LOAD DATA INFILE '/caminho/arquivo.csv'
REPLACE INTO TABLE usuarios
FIELDS TERMINATED BY ',';

-- Importar com ignore
LOAD DATA INFILE '/caminho/arquivo.csv'
IGNORE INTO TABLE usuarios
FIELDS TERMINATED BY ',';
```

### 🔒 Segurança

* `Backups e Restauração`

```bash
# Backup completo (com data)
mysqldump -u root -p --all-databases > backup_$(date +%Y%m%d).sql

# Backup de banco específico
mysqldump -u root -p meu_banco > backup.sql

# Backup com compressão
mysqldump -u root -p meu_banco | gzip > backup.sql.gz

# Restaurar backup
gunzip -c backup.sql.gz | mysql -u root -p meu_banco
```

* `Otimização`

```sql
-- Analisar tabela
ANALYZE TABLE nome_tabela;

-- Otimizar tabela
OPTIMIZE TABLE nome_tabela;

-- Verificar tabela
CHECK TABLE nome_tabela;

-- Reparar tabela
REPAIR TABLE nome_tabela;
```

### 🆘 Ajuda

```bash
# Ajuda geral
mysql --help

# Ajuda dentro do MySQL
HELP;
HELP SELECT;
HELP INSERT;

# Ajuda com conteúdo específico
? CREATE TABLE;
? SHOW;
```

### 📝 Aliases Úteis

Criar aliases no ~/.zshrc

```bash
# Aliases para MySQL
alias mysql-start="brew services start mysql"
alias mysql-stop="brew services stop mysql"
alias mysql-restart="brew services restart mysql"
alias mysql-status="brew services list | grep mysql"
alias mysql-connect="mysql -u root -p"
alias mysql-dump="mysqldump -u root -p"
alias mysql-import="mysql -u root -p"
alias mysql-backup="mysqldump -u root -p --all-databases > backup_\$(date +%Y%m%d).sql"
```

### 📋 Checklist Diário

| Comando | Descrição |
| ------- | --------- |
| `mysql -u root -p` | Conectar ao MySQL |
| `SHOW DATABASES;` | Ver bancos disponíveis |
| `USE nome_banco;` | Selecionar banco |
| `SHOW TABLES;` | Ver tabelas |
| `SELECT * FROM tabela LIMIT 10;` | Ver dados |
| `DESCRIBE tabela;` | Ver estrutura |

### 🎯 Resumo dos Comandos

| Categoria | Comandos Principais |
| --------- | ------------------- |
| **Conexão** | `mysql -u root -p` |
| **Banco** | `CREATE DATABASE`, `SHOW DATABASES`, `USE`, `DROP DATABASE` |
| **Tabela** | `CREATE TABLE`, `SHOW TABLES`, `DESCRIBE`, `ALTER TABLE`, `DROP TABLE` |
| **Dados** | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **Filtros** | `WHERE`, `ORDER BY`, `GROUP BY`, `HAVING`, `LIMIT` |
| **Joins** | `INNER JOIN`, `LEFT JOIN`, `RIGHT JOIN` |
| **Funções** | `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` |
| **Usuários** | `CREATE USER`, `GRANT`, `REVOKE`, `DROP USER` |
| **Exportar** | `mysqldump` |
| **Importar** | `mysql < arquivo.sql` |

### 📚 Referências

Documentação Oficial do MySQL
MySQL 8.0 Reference Manual
MySQL Cheat Sheet

Pronto para usar MySQL com confiança! 🐬🚀

---

## 📂 **Como salvar o arquivo**

```bash
# Criar a pasta mysql se não existir
mkdir -p ~/Desktop/comandos_dev/docs/mysql

# Salvar o arquivo
nano ~/Desktop/comandos_dev/docs/mysql/comandos-basicos.md

# Colar o conteúdo acima
# Salvar: Ctrl+O, Enter
# Sair: Ctrl+X
```
