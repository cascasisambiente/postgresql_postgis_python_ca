# User defined functions

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
Criar functions via pgadmin4


In Postgres, the main functional difference between a function and a stored procedure is that a function returns a result, whereas a stored procedure does not. This is because the intention behind a stored procedure is to perform some sort of activity and then finish, which would then return control to the caller.
