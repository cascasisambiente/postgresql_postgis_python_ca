# POSTGRESQL


PostgreSQL é uma SGBD SQL (Structured Query Language, 1974)

## RDBMS

Relational Database Management System

### Table

![image](https://user-images.githubusercontent.com/60735895/136801316-29c68da5-a564-4301-b83f-086389e3ecb8.png)

### Row

![image](https://user-images.githubusercontent.com/60735895/136801371-63444d9e-b6ab-4d73-86fb-eb07266269f3.png)

### Column

![image](https://user-images.githubusercontent.com/60735895/136801489-1ac6b9f4-70e5-4f7d-b015-ce6e03a58069.png)

### Null


## Comandos

### Categorias

#### DDL - Data Definition Language

| Commando | Descrição |
| ----------- | ----------- |
| CREATE | Cria uma noba tabela, view, ou outro objecto na base de dados |
| ALTER | Modifica um objecto existente na base de dados |
| DROP | Elimina um objecto existente na base de dados |

#### DML - Data Manipulation Language

| Commando | Descrição |
| ----------- | ----------- |
| SELECT | Devolve um conjunto de registos |
| INSERT | Insere um registo |
| UPDATE | Modifica um registo |
| DELETE | Elimina um registo |

#### DCL - Data Control Language

| Commando | Descrição |
| ----------- | ----------- |
| GRANT | Atribuí um privilégio a um utilizador/grupo |
| REVOKE | Retira um privilégio a um utilizador/grupo |


# RoadMap de modos de acesso

1. terminal
2. pgadmin4/datagrip
3. qgis
4. python

## Começar

### Aceder via terminal (psql)

```console
  psql --help
```
```console
  psql -h hostname(default=localhost) -p port(default=5432) -d databasename(default=local username) -U user(default=local username)
```
- hostname: postgresdatabase-do-user-4575137-0.db.ondigitalocean.com
- port: 25060
- database: defaultdb
- user: doadmin

```console
  psql -h postgresdatabase-do-user-4575137-0.db.ondigitalocean.com -p 25060 -d defaultdb -U doadmin
```

### Listar databases
```sql
  \l
```
### Conectar a outra base de dados
```sql
  \c
```
### Ajuda

```sql
  help
```

```sql
  \?
```

## Databases


```sql
  CREATE DATABASE teste;
```

```sql
  DROP DATABASE teste;
```

## Tables
```sql
  CREATE TABLE nome_da_tabela(
    nome_da_coluna_a(1) + tipo_de_dados_a(1) + constrains_a(0...n), 
    nome_da_coluna_b(1) + tipo_de_dados_b(1) + constrains_b(0...n), 
    .....
  );
```

```sql
  CREATE TABLE pessoa(
    id int,
    nome VARCHAR(100),
    ativo BOOLEAN,
    inicio_atividade TIMESTAMP,
    data_nacimento DATE    
  );
```

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
    data_nacimento DATE NOT NULL    
  );
```

```sql
  \d
```

# Tipo do dados

https://www.postgresql.org/docs/13/datatype.html

## Numéricos

| data type | descrição | min | max | exacto? |
| ----------- | ----------- | ----------- | ----------- | ----------- |
| int | inteiro | -2,147,483,648 | 2,147,483,647 | sim |


