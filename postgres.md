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


# Acesso

1. terminal
2. pgadmin4/datagrip
3. qgis
4. python

```console
  psql -h postgresdatabase-do-user-4575137-0.db.ondigitalocean.com -p 25060 -d defaultdb -U doadmin
```

```sql
  select * from table
```



