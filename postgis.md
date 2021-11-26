# Notas prévias

```sql
  SELECT name, default_version,installed_version
    FROM pg_available_extensions 
    WHERE name LIKE 'postgis%';
```

```sql
  CREATE EXTENSION postgis;
  CREATE EXTENSION postgis_raster;
  CREATE EXTENSION postgis_sfcgal;
```

```sql
  \dx postgis*
```

## postgis

https://www.ogc.org/

### Tipos de dados adicionais (geometry, geography e raster)

- **POINT** - uma coordenada única, habitualmente – mas não necessariamente - bidimensional
- **LINESTRING** - um conjunto de duas ou mais coordenadas, com interpretação linear do caminho entre as coordenadas
- **LINEARRING** - uma linestring de três ou mais coordenadas, em que os pontos de início e ﬁm são o mesmo
- **POLYGON** - um conjunto de um ou mais linearrings fechados, um anel exterior que deﬁne a área de fronteira e um conjunto de anéis interiores que deﬁne excepções (buracos) dentro dos limites de fronteira
- **MULTIPOINT** - conjunto de pontos - points
- **MULTILINESTRING** - conjunto de linhas - linestrings
- **MULTIPOLYGON** - conjunto de polígonos - polygons
- **GEOMETRYCOLLECTION** - conjunto heterogéneo de geometrias

### Standards

WKT / WBT

- POINT(0 0)
- POINT Z (0 0 0)
- POINT ZM (0 0 0 0)
- LINESTRING(0 0,1 1,1 2)
- POLYGON((0 0,4 0,4 4,0 4,0 0),(1 1, 2 1, 2 2, 1 2,1 1))
- MULTIPOINT((0 0),(1 2))
- MULTIPOINT Z ((0 0 0),(1 2 3))
- MULTILINESTRING((0 0,1 1,1 2),(2 3,3 2,5 4))
- MULTIPOLYGON(((0 0,4 0,4 4,0 4,0 0),(1 1,2 1,2 2,1 2,1 1)), ((-1 -1,-1 -2,-2 -2,-2 -1,-1 -1)))
- GEOMETRYCOLLECTION(POINT(2 3),LINESTRING(2 3,3 4))

Especificação OpenGIS precisa dum SRID para criar a geometria 

```sql
  bytea WKB = ST_AsBinary(geometry);
  text WKT = ST_AsText(geometry);
  geometry = ST_GeomFromWKB(bytea WKB, SRID);
  geometry = ST_GeometryFromText(text WKT, SRID);
```

- Output
    - binary: EWKB
    - ascii: HEXEWKB
- Input
    - binary: EWKB
    - ascii: HEXEWKB|EWKT


```sql
  INSERT INTO tabela (geom, nome)
    VALUES (ST_GeomFromText('POINT(-110258.656131153 -103959.586840153)', 3763), 'cascais_centroid');
```

- 3D
- 4D
- CIRCULARSTRING
- COMPOUNDCURVE
- CURVEPOLYGON
- MULTICURVE
- MULTISURFACE
- TRIANGLE
- TIN

### Geography

- geometry - plano (mais rápido, mais funções, menos rigoroso, mais espaço em disco)
- geograpfy - esfera (mais lento, menos funções, mais rigoroso, customizável, menos espaço em disco)
- rigor - geoide

Da documentação oficial:
![Screenshot from 2021-11-11 14-07-27](https://user-images.githubusercontent.com/60735895/141311901-5d55f6d9-5880-4967-b267-a93fb10b616c.png)


```sql
  \x
```

### TABELAS

#### SPATIAL_REF_SYS

```sql
  SELECT srid, auth_name, srtext
    FROM spatial_ref_sys
    WHERE srid in (3763, 4936, 4937, 4258, 4274, 27493, 4207, 5018, 20790, 4326) ;
```

#### GEOMETRY_COLUMNS

```sql
  SELECT *
    FROM geometry_columns;
```

### Criar tabelas espaciais


```sql
  CREATE TABLE tabela (
    id SERIAL, 
    nome text, 
    geom geometry(LINESTRING,4326)
  );
```

e / ou

```sql
  ALTER TABLE tabela 
    ADD COLUMN geom2 geometry(LINESTRING, 4326);
```

### Geometrias válidas

Geometrias tem de ser válidas e simples

#### POINT

Sempre simples


#### LINESTRING

Simples se não 'passar' no mesmo ponto duas vezes (excepto anéis)

![image](https://user-images.githubusercontent.com/60735895/141315794-96ad207a-8fda-4068-a11d-31a125660f96.png)

![image](https://user-images.githubusercontent.com/60735895/141315848-7e197cb9-90d0-4f3d-bf02-6d3902d39a28.png)


#### MULTILINESTRING

Simples se todos os elementos forem simples e as intersecções ocorrerem apenas em pontos

![image](https://user-images.githubusercontent.com/60735895/141316186-4496a5aa-d057-4d6a-b04d-370804223820.png)


#### POLYGON

É sempre simples
É válido se não existirem dois aneis (interior e exterior) na fronteira que se cruzam, sem linhas de corte e os aneis interiores têm de se encontrar totalmente 'dentro' do anel exterior

![image](https://user-images.githubusercontent.com/60735895/141316718-9819d24c-843f-4599-b401-93e3a8ee6e07.png)

![image](https://user-images.githubusercontent.com/60735895/141316821-927857a5-7293-4293-9c52-e35f10e1418f.png)


#### MULTIPOLYGON

Válido se todos os poligonos forem válidos, se não existirem intersecções e fronteriras partilhada apenas num número finito de pontos 

![image](https://user-images.githubusercontent.com/60735895/141317275-1db20656-02c9-4c24-8686-62a540c6459d.png)

<hr>

**ST_IsSimple()**
https://postgis.net/docs/ST_IsSimple.html

**ST_IsValid()**
https://postgis.net/docs/ST_IsValid.html

**ST_MakeValid**
https://postgis.net/docs/ST_MakeValid.html


```sql
  SELECT
    ST_IsValid('LINESTRING(0 0, 1 1)'),
    ST_IsSimple('LINESTRING(0 0, 0 0, 0 0)');
```

Por questões de performance não são validas as geometrias aquando do inserção (INSERT ou UPDATE).

* como validar todas as geometrias *

```sql
  ALTER TABLE tabela
    ADD CONSTRAINT validar_geometria
    CHECK (ST_IsValid(geom));
```

### Indíces espaciais


- **B-tree** (não espaciais, ordenamento fácil)
- **R-tree (GIST)** (espacial, postgis)
- **Q-tree** (espacial)
- **grids** (espaciais)

#### Criar indíces

2D

```sql
  CREATE INDEX index_name 
    ON tabela 
      USING GIST(geom);
```

Em ambiente de produção

```sql
  CREATE INDEX CONCURRENTLY index_name 
    ON tabela 
      USING GIST(geom);
```

```sql
  VACUUM ANALYSE tabela [geom];
```

btw

```sql
  VACUUM(FULL, ANALYZE, VERBOSE);
```

-- performance? à parte!?


## Topologia

3 conceitos base (2D geometry):

- Fronteira
- Interior
- Exterior

https://en.wikipedia.org/wiki/DE-9IM

- PONIT = 0
- LINE = 1
- AREA = 2
- VAZIO = F

![image](https://user-images.githubusercontent.com/60735895/141338211-d64e1264-35b1-4a74-9428-19ab9e939466.png)

 - ST_Contains
 - ST_Crosses
 - ST_Disjoint
 - ST_Equals
 - ST_Intersects
 - ST_Overlaps
 - ST_Touches
 - ST_Within
 - ST_Covers
 - ST_CoveredBy
 - ST_ContainsProperly


<hr>

## Funcionalidades adicionais de bases de dados geográficas

- **construction** - para construção de geometrias a partir de representações de texto e binárias
- **serialization** - para fazer output de geometrias em diversas representações de texto e binárias (tais como KML, GML, JSON e SVG)
- **predicates** - para testar relações entre geometrias e obter resultados de verdadeiro/falso
- **analysis and measurement** - para obter resumos numéricos acerca das geometrias (áreas, comprimentos, distâncias)
- **accessors** - para eliminação de partes de geometrias (tais como buracos de polígonos, vértices de linhas, etc.)
- **builders** - que usam inputs geométricos e geram novos outputs alterados (tais como unions, differences, buffers, convex hulls, etc.)
- **aggregates** - que a partir de conjuntos de geometrias devolvem resultados únicos (union é a mais comum)

<hr>


## Novas funções

```sql
  \df
```

# Usar postgis


```sql
  CREATE TABLE pontos (
    id BIGSERIAL NOT NULL PRIMARY KEY,
    nome VARCHAR(50),
    geom geometry(Point, 3763)
  );
```

```sql
  INSERT INTO pontos (nome, geom) 
    VALUES ('Mêda', ST_GeomFromText('POINT(73267 144418)'));
    
  INSERT INTO pontos (nome, geom) 
    VALUES ('Belmonte', ST_GeomFromText('POINT(66560.4 77001.8)'));
    
  INSERT INTO pontos (nome, geom) 
    VALUES ('Leiria', ST_GeomFromText('POINT(-57968.5 8687.1)'));
```

```sql
  SELECT st_srid(geom) FROM pontos;
```

```sql
  SELECT 
      geom, 
      ST_AsText(geom), 
      ST_AsEWKB(geom), 
      ST_AsEWKT(geom), 
      ST_AsTWKB(geom), 
      ST_X(geom), 
      ST_Y(geom), 
      ST_SRID(geom) 
    FROM pontos;
```

QGIS

<hr>


```console
  pg_dump -h training-do-user-4575137-0.b.db.ondigitalocean.com -p 25060 -U doadmin -FC defaultdb -v > dump.sql
```

```console
  pg_restore -h training-do-user-4575137-0.b.db.ondigitalocean.com -p 25060 -U doadmin -d rui dump.sql
```


## Análise espacial

```sql
  \d linha_agua_cascais
```

**ST_Length**  
https://postgis.net/docs/ST_Length.html

*Qual o comprimento de cada linha de água de Cascais, em km*


```sql
  SELECT nome, ST_Length(geom)/1000 as km 
    FROM linha_agua_cascais;
```


```sql
  SELECT nome, SUM(ST_Length(geom))/1000 as km 
    FROM linha_agua_cascais 
    GROUP BY nome;
```

*Qual o comprimento de todas as linha de água de Cascais, em km*


```sql
  SELECT SUM(ST_Length(geom))/1000 as km 
    FROM linha_agua_cascais;
```

```sql
  \d concelhos
```

**ST_Area**  
https://postgis.net/docs/ST_Area.html

*Qual a área em, km2, do distrito de Viseu (código do distrito = name_1)*

```sql
  SELECT SUM(ST_Area(geom))/1000000 as km2 
    FROM concelhos 
    WHERE name_1 = 'Viseu';
```

*Qual o nome e a área, em km2, do distrito com maior área (código do distrito = name_1)*


```sql
  SELECT name_1, SUM(ST_Area(geom))/1000000 km2
    FROM concelhos 
    GROUP BY name_1 
    ORDER BY km2 DESC 
    LIMIT 1;
```

**ST_Perimeter**  
https://postgis.net/docs/ST_Perimeter.html

*Qual o perímetro, em km arredondado à décima de unidade, do concelho de Cascais (código do concelho = name_2)*

```sql
  SELECT ROUND(SUM(ST_Perimeter(geom)/1000)::NUMERIC, 1) as km 
    FROM concelhos 
    WHERE name_2 = 'Cascais';
```


### Acrescentar geometrias

**ST_Centroid**  
https://postgis.net/docs/ST_Centroid.html

```sql
  ALTER TABLE concelhos 
    ADD COLUMN centroid geometry(Point, 3763);
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
    WHERE ST_Area(geom) / 1000000 > 1000;
```

*Quantos concelhos têm área superior à área do concelho de Cascais (código do concelho = name_2)*

```sql
  WITH cascais AS (
    SELECT ST_Area(geom) as area 
      FROM concelhos 
      WHERE name_2 = 'Cascais'
  )
  SELECT COUNT(*) 
    FROM concelhos, cascais 
    WHERE ST_Area(geom) > cascais.area;
```

```sql
  SELECT COUNT(*) 
    FROM concelhos c1, concelhos c2
    WHERE ST_Area(c1.geom) > ST_Area(c2.geom)
      AND c2.name_2 = 'Cascais';
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

**ST_Distance**  
https://postgis.net/docs/ST_Distance.html

*Qual a distância entre a Ribeira do Assobio (coluna nome) e o centroide de Cascais*

```sql
  SELECT ST_Distance(c.centroid, lac.geom) as m 
    FROM concelhos c, linha_agua_cascais lac 
    WHERE c.name_2 = 'Cascais' AND lac.nome = 'Ribeira do Assobio';
```


### minline, maxline, closestpoint

**ST_ShortestLine**  
https://postgis.net/docs/ST_ShortestLine.html

**ST_LongestLine**  
https://postgis.net/docs/ST_LongestLine.html

**ST_ClosestPoint**  
https://postgis.net/docs/ST_ClosestPoint.html


```sql
  CREATE TABLE minline AS (
    SELECT ST_ShortestLine(c.centroid, lac.geom), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      );
```

```sql
  \d minline
```

```sql
  CREATE TABLE maxline AS (
    SELECT ST_LongestLine(c.centroid, lac.geom), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      );
```

```sql
  CREATE TABLE closepoint AS (
    SELECT ST_ClosestPoint(lac.geom, c.centroid), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      );
```

```sql
  DROP TABLE minline;
  DROP TABLE maxline;
  DROP TABLE closepoint;
```

```sql
  CREATE TABLE minline AS (
    SELECT ST_ShortestLine(c.centroid, ST_UNION(lac.geom))::GEOMETRY('LINESTRING', 3763), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      GROUP BY lac.nome, c.name_1, c.centroid
      );
```

```sql
  CREATE TABLE maxline AS (
    SELECT ST_LongestLine(c.centroid, ST_UNION(lac.geom))::GEOMETRY('LINESTRING', 3763), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      GROUP BY lac.nome, c.name_1, c.centroid
      );
```

```sql
  CREATE TABLE closepoint AS (
    SELECT ST_ClosestPoint(ST_UNION(lac.geom), c.centroid)::GEOMETRY('POINT', 3763), c.name_1, lac.nome
      FROM concelhos c, linha_agua_cascais lac 
      WHERE c.name_2 = 'Cascais'
      GROUP BY lac.nome, c.name_1, c.centroid
      );
```

*Quais os concelhos do Distrito de Bragança que ficam afastados mais de 100km da rede ferroviária: Apresentar o nome e a distâcia em km"


```sql
  SELECT c.name_2, ST_Distance(c.centroid, r.geom) / 1000 km
    FROM concelhos c, railway r
    WHERE ST_Distance(c.geom, r.geom) / 1000 > 50
      AND c.name_1 = 'Bragança'
    ORDER BY km DESC;
```

```sql
  SELECT c.name_2, MIN(ST_Distance(c.centroid, r.geom) / 1000) km
    FROM concelhos c, railway r
    WHERE ST_Distance(c.geom, r.geom) / 1000 > 50
      AND c.name_1 = 'Bragança'
    GROUP BY c.name_2
    ORDER BY km DESC;
```

```sql
  SELECT c.name_2, MIN(ST_Distance(c.centroid, r.geom) / 1000) km
    FROM concelhos c, railway r
    WHERE ST_Distance(c.geom, r.geom) / 1000 > 50
      AND c.name_1 = 'Bragança'
    GROUP BY c.name_2
      HAVING COUNT(r.*) = (SELECT COUNT(*) from railway)
    ORDER BY km DESC;
```

**ST_Union**  
https://postgis.net/docs/ST_Union.html

**ST_Collect**  
https://postgis.net/docs/ST_Collect.html


ST_Collect, noemalmente é mais rápida que ST_Union  
ST_Collect agrega geometrias nuam coleção, sem modificar as mesmas. ST_Union cria geometrias novas


```sql
  SELECT ST_GeometryType(ST_Union(geom)), ST_GeometryType(ST_Collect(geom)) FROM railway;
```

```sql
  WITH rail AS (
    SELECT ST_Union(geom) geom FROM railway
  )
  SELECT c.name_2, ST_Distance(c.centroid, r.geom) / 1000 km
    FROM concelhos c, rail r
    WHERE ST_Distance(c.geom, r.geom) / 1000 > 50
      AND c.name_1 = 'Bragança'
    ORDER BY km DESC;
```

```sql
  WITH rail AS (
    SELECT ST_Collect(geom) geom FROM railway
  )
  SELECT c.name_2, ST_Distance(c.centroid, r.geom) / 1000 km
    FROM concelhos c, rail r
    WHERE ST_Distance(c.geom, r.geom) / 1000 > 50
      AND c.name_1 = 'Bragança'
    ORDER BY km DESC;
```

### buffer

**ST_Buffer**  
https://postgis.net/docs/ST_Buffer.html

*Criar um buffer de 500 m em cada linha de água*

multilinestring => buffer vai ser multipolygon

```sql
  ALTER TABLE linha_agua_cascais ADD COLUMN buffer GEOMETRY('MULTIPOLYGON', 3763);
 ```
 
 ```sql
  UPDATE linha_agua_cascais SET buffer = ST_Multi(ST_Buffer(geom, 500));
 ```

```sql
  \d edificios
 ```

*Quantas familias estão dentro do buffer das linhas de água*

```sql
  SELECT SUM(familias)::INT 
    FROM edificio e, linha_agua_cascais lac 
    WHERE ST_Within(e.geom, lac.buffer);
 ```
 
 
```sql
  WITH linhas_agua AS (
    SELECT ST_Union(buffer) buffer FROM linha_agua_cascais
  )
  SELECT SUM(familias)::INT 
    FROM edificio e, linhas_agua lac 
    WHERE ST_Within(e.geom, lac.buffer);
 ```
 
 ```sql
  WITH familias AS (
    SELECT DISTINCT e.id, e.familias 
      FROM edificio e, linha_agua_cascais lac
      WHERE ST_Within(e.geom, lac.buffer)
  )
  SELECT SUM(f.familias)::INT FROM familias f;
 ```
 
 ST_Intersects vs. ST_Within
 
  
**ST_Intersects**  
https://postgis.net/docs/ST_Intersects.html

 
**ST_Within**  
https://postgis.net/docs/ST_Within.html

 
*Quais os concelhos cujo centroíde está fora do concelho? Quais as distâncias, em metros, entre o centroide e o concelho* 

```sql
  SELECT name_2, ST_Distance(centroid, geom) as m 
    FROM concelhos 
    WHERE NOT ST_Intersects(centroid, geom);
```
    
*Quantos edificios por freguesia em Cascais*

```sql
  SELECT f.nome, COUNT(*) count
    FROM freguesia f, edificio e 
    WHERE ST_Intersects(f.geom, e.geom)
    GROUP BY f.nome;
 ```
 
*Quais as freguesias de Cascais com mais de 10000 edificios*

```sql
  SELECT f.nome, COUNT(*) count
    FROM freguesia f, edificio e 
    WHERE ST_Intersects(f.geom, e.geom)
      GROUP BY f.nome
    HAVING COUNT(*) > 10000;
 ```
 
 *Qual a freguesia de cada secao estatistica*
    
```sql
  SELECT f.nome, s.codigo
    FROM freguesia f, seccaoestatistica s
      WHERE ST_Intersects(s.geom, f.geom)
    ORDER BY s.codigo;
 ```
 
 ```sql
  SELECT 'seccaoestatistica intersects freguesia', count(*)
    FROM freguesia f, seccaoestatistica s
      WHERE ST_Intersects(s.geom, f.geom)
  UNION 
  SELECT '#seccaoestatistica', count(*) 
    FROM seccaoestatistica;
 ```
    
 se com várias freguesias (ST_Intersects vs. ST_Within vs. ST_Overlaps) border contact
 

*Área e percentagem da área da cada concelho"

 ```sql
  SELECT c.name_2, ST_Area(c.geom) / 1000000 , ST_Area(c.geom) * 100 /SUM(ST_Area(d.geom)) AS percentagem
    FROM concelhos c, concelhos d
    GROUP BY c.name_2, c.geom
    ORDER BY percentagem DESC;
 ```

 ```sql
 WITH portugal AS (
    SELECT ST_Union(geom) geom FROM concelhos
  )
  SELECT c.name_2, ST_Area(c.geom) / 1000000 , ST_Area(c.geom) * 100 /ST_Area(p.geom) AS percentagem
    FROM concelhos c, portugal p
    ORDER BY percentagem DESC;
 ```
 ### Percursos

*Ordenar as capitais de distrito por distância do seu centroid ao centroid do concelho de Cascais*

```sql
  SELECT c.name_2, ST_Distance(c.centroid, cascais.centroid)/1000 dist_km
    FROM concelhos c, concelhos cascais
    WHERE c.name_2 = c.name_1 AND cascais.name_2 = 'Cascais'
    ORDER BY dist_km ASC;
 ```
 
*Construir uma linha que una os pontos anteriores*
 
 ```sql 
   CREATE TABLE percurso AS
     WITH tabela_ordenada AS (
       SELECT c.name_2, ST_Distance(c.centroid, cascais.centroid)/1000 dist_km, c.centroid cent
         FROM concelhos c, concelhos cascais
         WHERE c.name_2 = c.name_1 AND cascais.name_2 = 'Cascais'
         ORDER BY dist_km ASC
      )
      SELECT 1 as id, ST_MakeLine(tabela_ordenada.cent) as geom
        FROM tabela_ordenada;
 ```

 **ST_MakeLine**  
https://postgis.net/docs/ST_MakeLine.html

 **ST_MakePolygon**  
https://postgis.net/docs/ST_MakePolygon.html

 
```sql
  SELECT c.name_2, ST_Distance(c.centroid, cascais.centroid)/1000 dist_km
    FROM concelhos c, concelhos cascais
    WHERE c.name_2 = c.name_1 AND cascais.name_2 = 'Cascais'
    ORDER BY dist_km ASC;
 ```
 
  **ST_NPoints**  
https://postgis.net/docs/ST_NPoints.html

  **ST_PointN**  
https://postgis.net/docs/ST_PointN.html

```sql
  SELECT ST_NPoints(geom), ST_PointN(geom, generate_series(1, ST_NPoints(geom)))
    FROM percurso;
 ```
 
 ### multi vs single
 
  
**ST_Multi**  
https://postgis.net/docs/ST_Multi.html

**ST_Dump**  
https://postgis.net/docs/ST_Dump.html
  
```sql
    SELECT ST_ASText(geom), ST_ASText(ST_Multi(geom))
      FROM pontos;
```

```sql
    SELECT ST_Dump(geom) 
      FROM freguesia;
```
 
```sql
    SELECT nome, (ST_Dump(geom)).path, (ST_Dump(geom)).geom::geometry('Polygon', 3763), ST_AsText((ST_Dump(geom)).geom)
      FROM freguesia;
```
 
**ST_NumGeometries**  
https://postgis.net/docs/ST_NumGeometries.html

**ST_GeometryN**  
https://postgis.net/docs/ST_GeometryN.html
 
 ```sql
  SELECT nome, ST_GeometryN(geom, generate_series(1, ST_NumGeometries(geom)))::geometry('Polygon', 3763)
    FROM freguesia;
```
 
 
 ### Simplificar geometrias
 
**ST_Simplify**  
https://postgis.net/docs/ST_Simplify.html

**ST_SimplifyPreserveTopology**  
https://postgis.net/docs/ST_SimplifyPreserveTopology.html
 
   ```sql
     CREATE VIEW freguesia_simplificada AS (
      SELECT nome, ST_Simplify(geom, 1000) FROM freguesia
     );
 ```
ST_SimplifyPreserveTopology evita erros topológicos (mas poderá criar polígonos sobrepostos), e preserva as ilhas pequenas, que desaparecem com ST_Simplify  




