

# Conceitos

## TABLE (tabela)

![image](https://user-images.githubusercontent.com/60735895/137928978-b9ea9c3a-4efc-4dea-93db-0c9f758faa4a.png)

## FIELD (campo)

## ROW/RECORD (registo)

![image](https://user-images.githubusercontent.com/60735895/137929167-5ca83e3b-27ca-4a5c-a412-9b57ad2acaa7.png)

## COLUMN (coluna)

![image](https://user-images.githubusercontent.com/60735895/137929259-ec7069d3-e786-49a3-973a-f08d457e036f.png)

## NULL (vazio)


# Comandos

DDL - Data Definition Language

| Commando | Descrição |
| ----------- | ----------- |
| CREATE | Cria uma nova tabela, view, ou outro objecto na base de dados |
| ALTER | Modifica um objecto existente na base de dados |
| DROP | Elimina um objecto existente na base de dados |

DML - Data Manipulation Language

| Commando | Descrição |
| ----------- | ----------- |
| INSERT | Insere um registo |
| UPDATE | Modifica um registo |
| DELETE | Elimina um registo |

DCL - Data Control Language

| Commando | Descrição |
| ----------- | ----------- |
| GRANT | Atribuí um privilégio a um utilizador/grupo |
| REVOKE | Retira um privilégio a um utilizador/grupo |

DQL - Data Query Language

| Commando | Descrição |
| ----------- | ----------- |
| SELECT | Devolve um conjunto de registos |


# MODOS DE ACESSO

1. terminal (psql)
2. pgadmin4/datagrip
3. qgis
4. python (psycopg2)

# ACEDER VIA TERMINAL (psql)

```console
  psql --help
```

```console
  psql -h hostname(default=localhost) -p port(default=5432) -d databasename(default=username) -U user(default=local username)
```

- hostname: training-do-user-4575137-0.b.db.ondigitalocean.comm
- port: 25060
- database: defaultdb
- user: doadmin

```console
  psql -h training-do-user-4575137-0.b.db.ondigitalocean.com -p 25060 -d defaultdb -U doadmin
```

Listar databases
```sql
  \l
```
Conectar a outra base de dados
```sql
  \c
```
## AJUDA


```sql
  \?
```

```sql
  \h NOME_DO_COMANDO
```

# DATABASES

Criar uma base de dados nova

```sql
  CREATE DATABASE teste;
```
Eliminar uma base de dados

```sql
  DROP DATABASE teste;
```

## Tables

Criar uma tabela nova

```sql
  CREATE TABLE nome_da_tabela(
    nome_da_coluna_a(1) + tipo_de_dados_a(1) + constrains_a(0...n), 
    nome_da_coluna_b(1) + tipo_de_dados_b(1) + constrains_b(0...n), 
    .....
  );
```

# Tipo do dados

https://www.postgresql.org/docs/13/datatype.html

## Numéricos

| data type | descrição | min | max | exacto? | tamanho |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| bigint | inteiro | -9,223,372,036,854,775,808 | 9,223,372,036,854,775,807 | sim | 8 bytes |
| int | inteiro | -2,147,483,648 | 2,147,483,647 | sim | 4 bytes |
| smalint | inteiro | -32,768 | 32,767| sim | 2 bytes |
| bigserial | inteiro auto incrementavél | 1 | 9,223,372,036,854,775,807 | sim | 8 bytes |
| serial | inteiro auto incrementavél | 1 | 2,147,483,647 | sim | 4 bytes |
| smallserial | inteiro auto incrementavél | 1 | 32,767 | sim | 2 bytes |
| decimal | decimal | -10^38 +1 | 10^38 -1 | sim | 2 variável |
| numeric | numeric | -10^38 +1 | 10^38 -1 | sim | 2 variável |
| real | real | -1.79E + 308 | 1.79E + 308 | não | 2 variável |
| float| double precision | -3.40E + 38 | 3.40E + 38 | não | 2 variável |


## Tempo

| data type | descrição | min | max | tamanho |
| ----------- | ----------- | ----------- | ----------- | ----------- | 
| timestamp | dia e hora sem timezone | 4713 AC | 294276 DC | 8 bytes |
| timestamp (tz) | dia e hora com timezone | 4713 AC | 294276 DC | 8 bytes |
| date | data | 4713 AC | 5874897 DC | 4 bytes |
| time | hora sem timezone | 00:00:00 | 24:00:00 | 8 bytes |
| time (tz) | hora com timezone | 00:00:00+1459 | 24:00:00-1459 | 12 bytes |
| interval | intervalo de tempo | -178000000 anos | 178000000 anos | 12 bytes |



## Binários

| data type | descrição | tamanho |
| ----------- | ----------- | ----------- | 
| bytea | string binária | 4 bytes + tamanho da string |


## Bolleano

| data type | descrição | tamanho |
| ----------- | ----------- | ----------- | 
| bollean | verdadeiros / falso | 1 byte |


## Texto

| data type | descrição | tamanho max |
| ----------- | ----------- | ----------- | 
| char | string tamanho fixo | 8000 caracteres |
| varchar | string tamanho variavél | 8000 caracteres |
| text | string tamanho variavél | 2,147,483,647 caracteres |


## Outros

| data type | descrição | 
| ----------- | ----------- |
| UUID | Universally Unique Identifiers | 
| XML | xml |
| JSON | json |
| Array | vector |


# Constrains

https://www.postgresql.org/docs/13/ddl-constraints.html

- NOT NULL
- DEFAULT
- UNIQUE
- PRIMARY KEY
- FOREIGN KEY
- CHECK
- INDEX


```sql
  CREATE TABLE pessoa(
    id int,
    nome VARCHAR(100),
    ativo BOOLEAN,
    inicio_atividade TIMESTAMP,
    data_nascimento DATE,
    email VARCHAR(200),
    local_nascimento VARCHAR(100)
  );
```
Descrever um objecto, por exemplo, uma tabela

```sql
  \d pessoa
```

```sql
  DROP TABLE pessoa;
  CREATE TABLE pessoa(
    id BIGSERIAL NOT NULL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    ativo BOOLEAN NOT NULL,
    inicio_atividade TIMESTAMP NOT NULL,
    data_nascimento DATE NOT NULL,
    email VARCHAR(200),
    local_nascimento VARCHAR(100),
    zona INT DEFAULT 25 CHECK (zona > -20)
  );
```

```sql
  \d
```
Descrever obejctos

- \dt -- lista tables
- \dn -- lista schema
- \df -- lista functions
- \dv -- lista views
- \du -- lista roles



## INSERT

Inserir um registo novo numa tabela

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) VALUES ('João Silva', true, now(), '2001-01-02', 'joao.silva@foo.com', 'Portugal');
```

Inserir vários registos numa tabela

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) VALUES ('Paulo Silva', false, now(), '1945-01-02', null, 'França'), ('Filipa Teixeira', true, now(), '1996-01-02', null, null);
```

Executar instruções SQL num ficheiro externo

```sql
  \i caminho_para_ficheiro/pessoa.sql
```

## SELECT

Selecionar todos os campo de todos os registos duma tabela

```sql
  SELECT * 
    FROM pessoa;
```

Selecionar vários campos de todos os registos duma tabela
```sql
  SELECT nome, ativo 
    FROM pessoa;
```

### Order by

Selecionar os registos duma tabela ordenados por uma deternimanda coluna

```sql
  SELECT * 
    FROM pessoa 
    ORDER BY data_nascimento;
```
Selecionar os registos duma tabela ordenados, de modo decrescente, por uma deternimanda coluna
```sql
  SELECT * 
    FROM pessoa 
    ORDER BY id DESC;
```

Selecionar os registos duma tabela ordenados por uma várias colunas
```sql
  SELECT * 
    FROM pessoa 
    ORDER BY ativo ASC, id DESC;
```

### Limit e offset

Limitar o número de registos devolvidos 

```sql
  SELECT * 
    FROM pessoa 
    LIMIT 2;
```

Limitar e deslocar o número de registos devolvidos 

```sql
  SELECT * 
    FROM pessoa 
    ORDER BY 1 ASC 
    OFFSET 5 LIMIT 3;
```

Limitar e deslocar o número de registos devolvidos (SQL válido, mas menos utilizado)

```sql
  SELECT * 
    FROM pessoa 
    ORDER BY id ASC 
    OFFSET 5 FETCH FIRST 3 ROW ONLY;
```

### Distinct

Devolver um campo (ou conjunto de campos) distintos

```sql
  SELECT DISTINCT(local_nascimento) 
    FROM pessoa 
    ORDER BY local_nascimento;
```

### Where

Filtar registos devolvidos por condição de um campo

```sql
  SELECT * 
    FROM pessoa 
    WHERE ativo = true;
```

Filtar registos devolvidos por condições de vários campos uilizando o operador AND
```sql
  SELECT * 
    FROM pessoa 
    WHERE ativo = true AND local_nascimento = 'China';
```

Filtar registos devolvidos por condições de vários campos uilizando o operador AND e OR
```sql
  SELECT * 
    FROM pessoa 
    WHERE (ativo = true AND local_nascimento = 'China')  
      OR data_nascimento > '2012-01-01';
```

### Comparation operators

Devolvem um booleano

```sql
  SELECT 1 = 1, 2 = 4;
```

```sql
  SELECT 2 > 2, 2 < 2, 2 <= 2, 2 >= 2, 2 != 2, 2 <> 2;
```

### In

Pesquisar num conjunto

```sql
  SELECT * 
    FROM pessoa 
    WHERE local_nascimento IN ('Poland', 'Canada');
```

### Between

Pesquisar num intervalo

```sql
  SELECT * 
    FROM pessoa 
    WHERE data_nascimento 
    BETWEEN '2001-01-01' and '2001-07-01';
```

### Like

Pesquisar correspondências em cadeias de texto

https://www.postgresql.org/docs/13/functions-matching.html

```sql
  SELECT * 
    FROM pessoa 
    WHERE email like 'wired.com';
```
- % (0+ caracteres)
- _ (1 caracter)


```sql
  SELECT * 
    FROM pessoa 
    WHERE email like '%wired.com%';
```

```sql
  SELECT * 
    FROM pessoa 
    WHERE email ilike '%WiRed.com';
```


# Timestamp / date

https://www.postgresql.org/docs/13/datatype-datetime.html

```sql
  SELECT NOW() as now, NOW()::DATE as date, NOW()::TIME as time;
```

```sql
  SELECT NOW() - INTERVAL '1 YEAR';
```

```sql
  SELECT NOW() - '2021-01-01 12:00:00';
```

```sql
  SELECT TO_CHAR(NOW(), 'YYYY'), TO_CHAR(NOW(), 'HH:MM:SS dd-mm-YYYY');
```

```sql
  SELECT nome, data_nascimento, AGE(NOW(), data_nascimento) FROM pessoa;
```

### Aggregate

Agregar resultados

```sql
  SELECT local_nascimento, COUNT(*) 
    FROM pessoa 
    GROUP BY local_nascimento;
```

```sql
  SELECT local_nascimento, COUNT(*) as count 
    FROM pessoa 
    GROUP BY local_nascimento 
    ORDER BY count DESC;
```

```sql
  SELECT local_nascimento, COUNT(*) as count 
    FROM pessoa 
    GROUP BY local_nascimento 
      HAVING COUNT(*) > 2 
    ORDER BY count DESC;
```

https://www.postgresql.org/docs/13/functions-aggregate.html


HAVING != WHERE

```sql
  CREATE TABLE carro(
    id BIGSERIAL NOT NULL PRIMARY KEY,
    fabricante VARCHAR(100) NOT NULL,
    modelo VARCHAR(100) NOT NULL,
    preco NUMERIC(9, 2) NOT NULL,
    CHECK (char_length(fabricante) >=3 ),
    CHECK (char_length(modelo) >=3 ),
    CHECK (preco >= 1000 )
  );
```

```sql
  \i caminho_para_ficheiro/carro.sql
```

```sql
  ALTER TABLE carro
    ADD CONSTRAINT unique_fabricante_modelo UNIQUE (fabricante, modelo);
```
Não existe alter constraint!!

```sql
  ALTER TABLE carro
    ALTER TABLE carro DROP CONSTRAINT carro_modelo_check;
```

```sql
  \i caminho_para_ficheiro/carro.sql
```


```sql
  SELECT MAX(preco), MIN(preco), AVG(preco) 
    FROM carro;
```

```sql
  SELECT MAX(preco), MIN(preco), ROUND(AVG(preco), 2) 
    FROM carro;
```

*No conjunto de carros, qual o valor mais elevado de cada fabricante?*

```sql
  SELECT fabricante, MAX(preco) 
    FROM carro 
    GROUP BY fabricante;
```

### Window functions

https://www.postgresql.org/docs/13/tutorial-window.html

```sql
  SELECT fabricante, modelo, MAX(preco) OVER (PARTITION BY fabricante) 
    FROM carro;
```

```sql
  SELECT fabricante, modelo, MAX(preco) OVER (PARTITION BY fabricante, modelo) 
    FROM carro;
```


### Operadores aritméticos

```sql
  SELECT 10 + 15 as soma, 10 - 15 as diferenca, 10 * 15 produto, 15/10 divisao, 15%10 resto, 15^10 potencia, 10! as fatorial;
```

```sql
  SELECT *, ROUND(preco * .1, 2) as desconto, preco - ROUND(preco * .1, 2) as novo_preco FROM carro;
```

# Null

https://www.postgresql.org/docs/13/functions-conditional.html

## COALESCE

Valores pré-definidos - devolve o primeiro valor não null

```sql
  SELECT COALESCE(null, 1);
```

```sql
  SELECT COALESCE(null, null, 10, 1);
```

```sql
  SELECT COALESCE(email, 'sem email') 
    FROM pessoa;
```

## NULLIF

```sql
  SELECT 1/0;
```

NULLIF(v1, v2) devolve null se v1 = v2, caso contrário devolve v1

```sql
  SELECT NULLIF(1, 0), NULLIF(1, 1), NULLIF(12, 7);
```

```sql
  SELECT 1 / NULLIF(0, 0);
```

```sql
  SELECT nome, 1000/zona 
    FROM pessoa;
```

```sql
  UPDATE pessoa SET zona = 0 WHERE id%3 = 0;
```

```sql
  SELECT nome, 1000/zona 
    FROM pessoa;
```

*Null se zona = 0?*

```sql
  SELECT nome, COALESCE(1000 / NULLIF(zona, 0), NULL) 
    FROM pessoa;
```

# CASE

Igual a if-then-else noutras linguagens de programação

```sql
  SELECT nome, 
         CASE 
            WHEN AGE(NOW(), data_nascimento) > '76 years' THEN 'Sénior'
            WHEN AGE(NOW(), data_nascimento) > '18 years' THEN 'Adulto'
            ELSE 'Criança'
         END
    FROM pessoa;
```

# GREATEST e LEAST

Maior e menor entre conjuntos com dados ordenáveis entre si

```sql
  SELECT GREATEST(zona, id), LEAST(zona, id), zona, id 
    FROM pessoa
    ORDER BY id;
```

# Primary keys

Identificam univocamente um registo 

- id
- email, etc
- conjunção
- uuid (útil para exportações)

```sql
  \d pessoa
```

```sql
  SELECT * 
    FROM pessoa_id_seq;
```

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) 
    VALUES ('João Silva', true, now(), '2001-01-02', 'joao.silva2@foo.com', 'Portugal');
```

```sql
  INSERT INTO pessoa (
    id,
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) 
    VALUES (1, 'João Silva', true, now(), '2001-01-02', 'joao.silva3@foo.com', 'Portugal');
```

# ALTER 

```sql
  ALTER TABLE pessoa 
    DROP CONSTRAINT pessoa_pkey;
```

```sql
  \d pessoa
```

```sql
  INSERT INTO pessoa (
    id,
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) 
    VALUES (1, 'João Silva', true, now(), '2001-01-02', 'joao.silva3@foo.com', 'Portugal');
```

```sql
  SELECT * 
    FROM pessoa WHERE id = 1;
```

```sql
  ALTER TABLE pessoa 
    ADD PRIMARY KEY (nome, email);
```

```sql
  DELETE 
    FROM pessoa 
    WHERE id = 1;
```

```sql
  ALTER TABLE pessoa 
    ADD PRIMARY KEY (id);
```

```sql
  \d pessoa
```

# Constrains em maior detalhe

## Unique


```sql
  Select email, COUNT(*) 
    FROM pessoa 
    GROUP BY email 
      HAVING COUNT(*) > 1;
```

Define que o valor dum campo duma coluna é único em todos sos registos da tabela  
Valores NULL não são considerados (usar NOT NULL, se necessário)  
Cria automaticamente um índice  

```sql
  ALTER TABLE pessoa 
    ADD CONSTRAINT unique_email_adress 
      UNIQUE (email);
```

```sql
  ALTER TABLE pessoa 
    ADD CONSTRAINT unique_nome_local_nascimento_adress 
      UNIQUE (nome, local_nascimento);
```

Nome automático:

```sql
  ALTER TABLE pessoa 
    ADD UNIQUE (nome, local_nascimento);
```

## Check


```sql
  ALTER TABLE pessoa 
    ADD COLUMN distrito VARCHAR(50);
```

```sql
  UPDATE pessoa SET distrito = 'Lisboa' WHERE id % 3 = 0;
  UPDATE pessoa SET distrito = 'Braga' WHERE id % 3 = 1;
  UPDATE pessoa SET distrito = 'Guarda' WHERE id % 3 = 2;
```

```sql
  SELECT DISTINCT distrito 
    FROM pessoa;
```

Verifica condições definidas  
Podemos relacionar multiplas colunas  

```sql
  ALTER TABLE pessoa 
    ADD CONSTRAINT distrito_check_constraint 
      CHECK(distrito = 'Lisboa' OR distrito = 'Braga' OR distrito = 'Guarda');
```

```sql
  UPDATE pessoa 
    SET distrito = 'Porto' 
      WHERE id % 3 = 0;
```

# Delete

Usar WHERE (quase) sempre!!  
Não existe undo  


```sql
  DELETE FROM pessoa 
    WHERE distrito = 'Braga';
```

# Update

Usar WHERE (quase) sempre!!  
Não existe undo  


```sql
  UPDATE pessoa 
    SET email = 'aaa@bbb.ccc' 
      WHERE id IN (12, 15, 18);
```

```sql
  UPDATE pessoa 
    SET email = 'ddd@eee.fff', local_nascimento = 'ggg' 
      WHERE id IN (11, 14, 17, 20, 23);
```

# Exceptions

Prever erros e definir comportamentos adequados  

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento,
    distrito
  ) 
    VALUES ('João', true, now(), '2001-01-02', 'joao@foo.com', 'Portugal', 'Braga');
```
x2

## On conflict

Erros de integridade  


https://www.postgresql.org/docs/13/sql-insert.html

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento,
    distrito
  ) 
    VALUES ('João', true, now(), '2001-01-02', 'joao.silva@foo.com', 'Portugal', 'Braga')
    ON CONFLICT (nome, local_nascimento) 
      DO NOTHING;
```

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento,
    distrito
  ) 
    VALUES ('João', true, now(), '2001-01-02', 'joao.silva@foo.com', 'Portugal', 'Braga')
    ON CONFLICT (nome, local_nascimento) 
      DO UPDATE 
        SET nome = 'António', local_nascimento=EXCLUDED.local_nascimento;
```


# Foreign keys

Relacionar duas tabelas  
Uma FOREIGN KEY tem de estar associada a uma PRIMARY KEY de outra tabela  


```sql
  ALTER TABLE pessoa 
    ADD COLUMN carro_id BIGINT REFERENCES carro (id);
```

```sql
  UPDATE pessoa 
    SET carro_id = 18500 
    WHERE id = 12; 
```

```sql
  UPDATE pessoa 
    SET carro_id = 3 
    WHERE id = 12; 
```

```sql
  SELECT * 
    FROM pessoa 
    WHERE id = 12;
```

```sql
  UPDATE pessoa SET carro_id = 1 WHERE id = 23; 
  UPDATE pessoa SET carro_id = 7 WHERE id = 15; 
  UPDATE pessoa SET carro_id = 2 WHERE id = 5;
  UPDATE pessoa SET carro_id = 4 WHERE id IN (26, 14, 18);
```

```sql
  SELECT * 
    FROM pessoa 
    WHERE carro_id IS NOT NULL;
```

## JOINS

Resultados da query apresentam a "união" de duas tabelas

### Inner joins

```sql
  SELECT * 
    FROM pessoa 
    INNER JOIN carro 
      ON pessoa.carro_id = carro.id;
```
Sintaxe alternativa

```sql
  SELECT * 
    FROM pessoa, carro 
    WHERE pessoa.carro_id = carro.id;
```

INNER JOIN == JOIN

```sql
  SELECT * 
    FROM pessoa 
    JOIN carro 
      ON pessoa.carro_id = carro.id;
```

#### Alias

Nomeação (AS)

```sql
  SELECT * 
    FROM pessoa AS p 
    JOIN carro AS c 
      ON p.carro_id = c.id;
```

```sql
  SELECT p.nome, p.data_nascimento, c.fabricante, c.modelo 
    FROM pessoa p 
    JOIN carro c 
      ON p.carro_id = c.id;
```

### Outros joins

```sql
  SELECT p.nome, c.fabricante, c.modelo 
    FROM pessoa p 
    LEFT JOIN carro c 
      ON p.carro_id = c.id;
```

```sql
  SELECT p.nome, c.fabricante, c.modelo 
    FROM pessoa p 
    RIGHT JOIN carro c 
      ON p.carro_id = c.id;
```

```sql
  SELECT p.nome, c.fabricante, c.modelo 
    FROM pessoa p 
    FULL OUTER JOIN carro c 
      ON p.carro_id = c.id;
```


![image](https://user-images.githubusercontent.com/60735895/137899416-6f32ecea-072a-4ab0-84e6-5543a0fd65a8.png)



## Comportamentos das relações entre tabelas após alterações

```sql
  DELETE from carro 
    WHERE id = 1;
```

```sql
  DELETE from pessoa 
    WHERE id = 23;
```

```sql
  DELETE from carro 
    WHERE id = 1;
```
Tipos de comportamentos 
- RESTRICT
- CASCADE
- SET NULL
- SET DEFAULT
- NO ACTION (potenciais problemas de integridade de dados)

Tipos de acções
- ON DELETE
- ON UPDATE
- ON INSERT


# Sequences

Normalmente utilizados para guardar os valores para a atribuição automatica de ids

```sql
  \d pessoa;
```

```sql
  \d pessoa_id_seq
```

```sql
  SELECT * 
    FROM pessoa_id_seq;
```

```sql
  SELECT nextval('pessoa_id_seq'::regclass);
```

```sql
  SELECT nextval('pessoa_id_seq'::regclass);
```

```sql
  INSERT INTO pessoa (
      nome,
      ativo,
      inicio_atividade,
      data_nascimento,
      email,
      local_nascimento,
      distrito
    ) 
      VALUES ('Paulo Silva', true, now(), '2001-01-02', 'joao.silva@foo.com', 'Portugal', 'Braga');
```

```sql
  SELECT * 
    FROM PESSOA 
    ORDER BY id DESC 
    LIMIT 2;
```

```sql
  ALTER SEQUENCE pessoa_id_seq 
    RESTART WITH 250;
```

# MIX

## Union

```sql
  SELECT id, nome FROM pessoa 
    UNION (ALL) 
    SELECT id, modelo 
      FROM carro;
```

ALL não remove duplicados


## With


```sql
  WITH cte(var1, var2) AS ( VALUES(48, ' string') )
    SELECT id * var1, CONCAT(nome, var2),  zona * var1 
      FROM pessoa, cte;
```

```sql
  WITH cte AS (SELECT 48 as var1, ' string' AS var2)
    SELECT id * var1, CONCAT(nome, var2),  zona * var1 
      FROM pessoa, cte;
```


## INDEX

Optimizar performance das consultas, potencialmente à custa da performance das restantes operações!

```console
  psql -h hostname(default=localhost) -p port(default=5432) -d databasename(default=username) -U user(default=local username)
```

- hostname: postgresdatabase-do-user-4575137-0.db.ondigitalocean.com
- port: 25060
- database: defaultdb
- user: training
- password: a mesma!!!

```sql
  SELECT count(*) n 
    FROM posicoes_posicao;
```

```sql
  SELECT * 
    FROM posicoes_posicao
    ORDER BY time ASC
    LIMIT 1;
```

```sql
  SELECT * 
    FROM posicoes_posicao
    WHERE poistion_id = 1234414;
```

```sql
  SELECT * 
    FROM posicoes_posicao
    ORDER BY viatura_id, time ASC
    LIMIT 100;
```

Preços a pagar:
- Espaço em disco($)
- Potencial menor performance na operação de INSERT
- DELETE pode ser impraticável se tabela muito grande com fk's
- Apenas usar em tabelas grandes
- Índices espaciais são caso à parte (conceito de ordenação)
- btree => tabela de 1 trilhião (10E12) => avg de 5*10E11 para 13, max de 10E12 para 14 


```sql
  CREATE INDEX index_name 
    ON table_name ( column1, column2.....);
```


## Export

Guarda o resultado duma query num ficheiro de texto

https://www.postgresql.org/docs/13/sql-copy.html

```sql
  SELECT p.nome, c.fabricante, c.modelo 
    FROM pessoa p 
      FULL OUTER JOIN carro c ON p.carro_id = c.id;
```

```sql
  \copy (SELECT p.nome, c.fabricante, c.modelo FROM pessoa p FULL OUTER JOIN carro c ON p.carro_id = c.id) TO '/home/rui/Desktop/filename.csv' DELIMITER ',' CSV HEADER;
```


## Views

Visualização, automaticamente atualizável, duma query especifica

```sql
  CREATE VIEW pessoa_carro AS 
    SELECT p.id, p.nome, c.fabricante, c.modelo 
      FROM pessoa p 
      JOIN carro c 
        ON p.carro_id = c.id; 
```

```sql
  SELECT * 
    FROM pessoa_carro; 
```

```sql
  UPDATE pessoa 
    SET carro_id = 2 
    WHERE id = 18; 
```

```sql
  SELECT * 
    FROM pessoa_carro; 
```

## Transactions

```sql
  BEGIN;
    instruções;
  COMMIT;
```


## Extensions

```sql
  SELECT * 
    FROM pg_available_extensions;
```

## FUNCTION

```sql
  CREATE FUNCTION nome_da_funcao(parametro1 tipo, parametro2 tipo)
    RETURNS tipo AS
      BEGIN
       -- logica
      END;
    LANGUAGE nome_da_linguagem;
```

```sql
  CREATE FUNCTION incrementa(val INTEGER) 
    RETURNS INTEGER AS $$
      BEGIN
        RETURN val + 1;
      END; $$
    LANGUAGE PLPGSQL;
```


## TRIGGER

Função invocada automaticamente quando ocorre um evento, associado a uma tabela:
- INSERT
- UPDATE
- DELETE
- TRUNCATE (não SQL)

Dois tipos de invocações:
- FOR EACH ROW
  - Podem devolver TABLE ROW
  - Usar WHEN
- FOR EACH STATEMENT
  - Devolvem NULL

Dois momentos de invocação:
- BEFORE
- AFTER
- INSTEAD OF

Acções sobre os triggers

- CREATE TRIGGER
- DROP TRIGGER
- ALTER TRIGGER
- DISABLE TRIGGER
- ENABLE TRIGGER


```sql
  CREATE FUNCTION trigger_function() 
    RETURNS TRIGGER 
    LANGUAGE PLPGSQL
      AS $$
        BEGIN
          -- logica do trigger
        END;
      $$
```

TriggerData;
- OLD
- NEW
- TG_
- TG_WHEN
- TG_TABLE_NAME


```sql
  CREATE TRIGGER trigger_name
    {BEFORE | AFTER} { event }
    ON tabela
    [FOR [EACH] { ROW | STATEMENT }]
       EXECUTE PROCEDURE trigger_function;
```

```sql
  ALTER TABLE pessoa
    ADD COLUMN
      idade_anos INTERVAL;
```

```sql
  CREATE OR REPLACE FUNCTION mudar_data_nascimento()
    RETURNS TRIGGER 
    LANGUAGE PLPGSQL
      AS $$
        BEGIN
	        IF NEW.data_nascimento != OLD.data_nascimento THEN
		        UPDATE pessoa SET idade_anos = AGE(now(), NEW.data_nascimento);
	      END IF;
	      RETURN NEW;
      END;
    $$;
```

```sql
  CREATE TRIGGER pessoa_mudar_data_nascimento
    AFTER UPDATE
    ON pessoa
    FOR EACH ROW
      EXECUTE PROCEDURE mudar_data_nascimento();
```

```sql
  UPDATE pessoa 
    SET data_nascimento = '2000-01-01' WHERE id = 1;
```

```sql
  SELECT id, data_nascimento, idade_anos 
    FROM pessoa
```

```sql
  CREATE OR REPLACE FUNCTION mudar_data_nascimento()
    RETURNS TRIGGER 
    LANGUAGE PLPGSQL
      AS $$
        BEGIN
	        IF NEW.data_nascimento != OLD.data_nascimento THEN
            RAISE NOTICE '%', NEW.id;
		        UPDATE pessoa SET idade_anos = AGE(now(), NEW.data_nascimento) WHERE id = NEW.id;
	      END IF;
	      RETURN NEW;
      END;
    $$;
```

```sql
  UPDATE pessoa 
    SET data_nascimento = '2010-01-01' WHERE id = 1;
```

```sql
  SELECT id, data_nascimento, idade_anos 
    FROM pessoa;
```


```sql
  ALTER TABLE tabela
    DISABLE TRIGGER trigger_name | ALL;
```

```sql
  ALTER TABLE tabela
    ENABLE TRIGGER trigger_name | ALL;
```

#### TRIGGER para logs

```sql
  CREATE TABLE pessoa_log(
  	id BIGSERIAL PRIMARY KEY,
	time TIMESTAMP NOT NULL,
	username TEXT NOT NULL,
	pessoa_id BIGINT NOT NULL,
	old_nome VARCHAR(100),
	old_ativo BOOLEAN,
	old_data_nascimento DATE,
	old_email VARCHAR(200),
	old_local_nascimento VARCHAR(100),
	old_zona INT,
	old_idade_anos INTERVAL,
	new_nome VARCHAR(100),
	new_ativo BOOLEAN,
	new_data_nascimento DATE,
	new_email VARCHAR(200),
	new_local_nascimento VARCHAR(100),
	new_zona INT,
	new_idade_anos INTERVAL
  );
```


```sql
  CREATE OR REPLACE FUNCTION log_pessoa()
    RETURNS TRIGGER 
    LANGUAGE PLPGSQL
      AS $$
        BEGIN
          INSERT INTO pessoa_log (
	    time, 
	    username, 
	    pessoa_id, 
	    old_nome, 
	    old_ativo, 
	    old_data_nascimento, 
	    old_email,
	    old_local_nascimento, 
	    old_zona, 
	    old_idade_anos, 
	    new_nome, 
	    new_ativo, 
	    new_data_nascimento, 
	    new_email, 
	    new_local_nascimento, 
	    new_zona, 
	    new_idade_anos
	  )
	    VALUES (
	      now(), 
	      current_user, 
	      COALESCE(NEW.id, OLD.id), 
	      OLD.nome, 
	      OLD.ativo, 
	      OLD.data_nascimento, 
	      OLD.email, 
	      OLD.local_nascimento,
	      OLD.zona, 
	      OLD.idade_anos, 
	      NEW.nome, 
	      NEW.ativo, 
	      NEW.data_nascimento, 
	      NEW.email,
	      NEW.local_nascimento,
	      NEW.zona,
	      NEW.idade_anos
	    );  
	  RETURN NEW;
        END;
      $$;
```

```sql
  CREATE TRIGGER log_pessoa_trigger
    AFTER UPDATE OR DELETE OR INSERT
    ON pessoa
    FOR EACH ROW
      EXECUTE PROCEDURE log_pessoa();
```

```sql
  INSERT INTO pessoa (
    nome,
    ativo,
    inicio_atividade,
    data_nascimento,
    email,
    local_nascimento
  ) VALUES ('João Silva', true, now(), '2001-01-02', 'joao.silva@foo.com', 'Portugal');

```

```sql
  SELECT * 
  	FROM pessoa_log; 
```

```sql
  DELETE 
    FROM pessoa
      WHERE id = 1;
```

```sql
  SELECT * 
  	FROM pessoa_log; 
```

```sql
  UPDATE pessoa 
    SET nome = 'Joana Fonseca da Silva Almeida', local_nascimento = 'Grécia' 
    	WHERE id in (3, 5, 7);
```

```sql
  SELECT * 
  	FROM pessoa_log; 
```


# PGADMIN4


