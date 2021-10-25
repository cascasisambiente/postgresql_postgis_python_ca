# postgis

## Tipos de dados adicionais (geometry, geography e raster)

- **POINT** - uma coordenada única, habitualmente – mas não necessariamente - bidimensional
- **LINESTRING** - um conjunto de duas ou mais coordenadas, com interpretação linear do caminho entre as coordenadas
- **LINEARRING** - uma linestring de três ou mais coordenadas, em que os pontos de início e ﬁm são o mesmo, habitualmente usada para desenhar polígonos
- **POLYGON** - um conjunto de um ou mais linearrings fechados, um anel exterior que deﬁne a área de fronteira e um conjunto de anéis interiores que deﬁne excepções (buracos) dentro dos limites de fronteira
- **MULTIPOINT** - conjunto de pontos - points
- **MULTILINESTRING** - conjunto de linhas - linestrings
- **MULTIPOLYGON** - conjunto de polígonos - polygons
- **GEOMETRYCOLLECTION** - um conjunto heterogéneo de geometrias

## Novas funções

## Indíces espaciais mais eficientes


- **B-tree** (não espaciais, ordenamento fácil)
- **R-tree** (espacial, postgis)
- **Q-tree** (espacial)
- **grids** (espaciais)


## Funcionalidades adicinais de bases de dados geográficas

- **construction** - para construção de geometrias a partir de representações de texto e binárias
- **serialization** - para fazer output de geometrias em diversas representações de texto e binárias (tais como KML, GML, JSON e SVG)
- **predicates** - para testar relações entre geometrias e obter resultados de verdadeiro/falso
- **analysis and measurement** - para obter resumos numéricos acerca das geometrias (áreas, comprimentos, distâncias)
- **accessors** - para eliminação de partes de geometrias (tais como buracos de polígonos, vértices de linhas, etc.)
- **builders** - que usam inputs geométricos e geram novos outputs alterados (tais como unions, differences, buffers, convex hulls, etc.)
- **aggregates** - que a partir de conjuntos de geometrias devolvem resultados únicos (union é a mais comum)

<hr>

# Usar postgis

```sql
  CREATE EXTENSION postgis;
```

```sql
  ALTER EXTENSION postgis UPDATE;
```

Criar tabela

```sql
  CREATE TABLE points (
    id BIGSERIAL NOT NULL PRIMARY KEY,
    nome VARCHAR(50),
    geom geometry(Point, 4326)
  );
```

```sql
  INSERT INTO points (nome, geom) 
    VALUES ('p1', ST_GeomFromText('POINT(0 0)'));
    
  INSERT INTO points (nome, geom) 
    VALUES ('p2', ST_GeomFromText('POINT(5 0)'));
    
  INSERT INTO points (nome, geom) 
    VALUES ('p3', ST_GeomFromText('POINT(0 5)'));
```

```sql
  \d geometry_columns
```

```sql
  SELECT * FROM geometry_columns;
```

```sql
  SELECT st_srid(geom) FROM points;
```

```sql
  \d spatial_ref_sys
```

```sql
  SELECT * FROM spatial_ref_sys;
```

## Análise espacial

https://postgis.net/docs/index.html


*Qual o comprimento de cada linha de água de Cascais, em km*

```sql
  SELECT nome, SUM(ST_Length(geom))/1000 as km 
    FROM linha_agua_cascais 
    WHERE nome IS NOT NULL 
    GROUP BY nome;
```

!!!!

```sql
  SELECT nome, SUM(ST_Length(ST_Transform(geom, 3763)))/1000 as km 
    FROM linha_agua_cascais 
    GROUP BY nome;
```

*Qual o comprimento de todas as linha de água de Cascais, em km*


```sql
  SELECT SUM(ST_Length(ST_Transform(geom, 3763)))/1000 as km 
    FROM linha_agua_cascais;
```

*Qual a área em, km2, do distrito de Viseu (código do distrito = name_1)*


```sql
  SELECT SUM(ST_Area(ST_Transform(geom, 3763)))/1000000 as km2 
    FROM concelhos 
    WHERE name_1 = 'Viseu';
```

*Qual o nome e a área, do distrito com maior área (código do distrito = name_1)*


```sql
  SELECT name_1, SUM(ST_Area(ST_Transform(geom, 3763)))/1000000 as km2 
    FROM concelhos 
    GROUP BY name_1 
    ORDER BY km DESC 
    LIMIT 1;
```

*Qual o perímetro, em km arredondado à décima de unidade, do concelho de Cascais (código do concelho = name_2)*

```sql
  SELECT ROUND(SUM(ST_Perimeter(ST_Transform(geom, 3763)))/1000, 1) as km 
    FROM concelhos 
    WHERE name_2 = 'Cascais';
```

```sql
  SELECT ROUND(SUM(ST_Perimeter(ST_Transform(geom, 3763)))::NUMERIC/1000, 1) as km 
    FROM concelhos 
    WHERE name_2 = 'Cascais';
```

### Representação de geometrias

```sql
  SELECT 
      ST_AsTEXT(ST_Centroid(geom)), 
      ST_AsEWKT(ST_Centroid(geom)), 
      ST_X(ST_Centroid(geom)), ST_Y(ST_Centroid(geom)) 
    FROM concelhos 
    WHERE name_2 = 'Cascais';
```

### Acrescentar geometrias

```sql
  ALTER TABLE concelhos 
    ADD COLUMN centroid geometry(Point, 4326);
```

*Preencher coluna centroid*

```sql
  UPDATE concelhos 
    SET centroid = ST_Centroid(geom);
```

*Quantos concelhos têm área superior a 1000km2 (código do concelho = name_2)*

```sql
  SELECT COUNT(*) 
    FROM concelhos 
    WHERE ST_Area(ST_Transform(geom, 3763)) / 1000000 > 1000;
```

*Quantos concelhos têm área superior à área do concelho de Cascais (código do concelho = name_2)*

```sql
  WITH cascais AS (
    SELECT ST_Area(ST_Transform(geom, 3763)) as area 
      FROM concelhos 
      WHERE name_2 = 'Cascais'
  )
  SELECT COUNT(*) 
    FROM concelhos, cascais 
    WHERE ST_Area(ST_Transform(geom, 3763)) > cascais.area;
```

*Ordenar os distritos alfabeticamente (código do distrito = name_1)*

```sql
  SELECT DISTINCT(name_1) 
    FROM concelhos 
    ORDER BY name_1;
```

```sql
  SELECT name_1 
    FROM concelhos 
    GROUP BY name_1 
    ORDER BY name_1;
```

*Qual a distância entre a Ribeira do Assobio (coluna nome) e o centroide de Cascais*

```sql
  SELECT ST_Distance(ST_Transform(c.centroid, 3763), ST_Transform(lac.geom, 3763)) as m 
    FROM concelhos c, linha_agua_cascais lac 
    WHERE c.name_2 = 'Cascais' AND lac.nome = 'Ribeira do Assobio';
```


### minline, maxline, closestpoint


*Quais os concelhos cujo centroíde está fora do concelho, e as distâncias, em metros, entre o centroide e o concelho* 

```sql
  SELECT name_2, ST_Distance(ST_Transform(centroid, 3763), ST_Transform(geom, 3763)) as m 
    FROM concelhos 
    WHERE NOT ST_Intersects(centroid, geom);
```

