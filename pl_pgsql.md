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

# Estrutura PL/pgSQL

```sql
  [ <<label>> ]
  [ DECLARE
      declarations ]
  BEGIN
      statements
  END [ label ];
```

```sql
  DECLARE
      var_prestador TEXT;
      var_gestor TEXT;
  BEGIN
    CASE WHEN NEW.codigo_da_ IS NULL AND NEW.tipo_parce != 'Parque Infantil' THEN
      NEW.prestador = NULL;
      NEW.gestor = NULL;
    ELSE
      CASE WHEN NEW.tipo_parce = 'Parque Infantil' THEN
        NEW.prestador = 'FLG';
        NEW.gestor = 'Fernanda Rodrigues';
      ELSE
          SELECT f.prestador, g.gestor INTO var_prestador, var_gestor
            FROM epvu.codigo_folhas as f JOIN epvu.gestores_freguesia as g
                ON (f.freguesia = g.freguesia) WHERE f."código" = NEW.codigo_da_ LIMIT 1;
          NEW.prestador = var_prestador;
          NEW.gestor = var_gestor;
      END CASE;
    END CASE;

    RETURN NEW;
  END;
```



