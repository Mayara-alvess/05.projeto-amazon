# Consultas SQL

Base de Dados contém 1469 linhas na tabela origem_produto e 1465 na tabela origem_review.

Consulta para identificar valores nulos, identifiquei na coluna about_product 4 valores nulos.

```jsx
SELECT COUNT (*)

 FROM `amazonsales-425320.origem.produto` 

 WHERE product_id IS NULL OR product_name IS NULL OR category IS NULL OR discounted_price IS NULL OR actual_price IS NULL OR discount_percentage IS NULL OR about_product IS NULL
```

Consulta para identificar valores nulos, encontrei 468 na coluna img_link 466 na coluna  product_link e 2 na rating_count.

```jsx
SELECT COUNT (*)
 FROM `amazonsales-425320.origem.review` 
 WHERE product_id IS NULL OR user_id IS NULL OR user_name IS NULL OR review_id IS NULL OR review_title IS NULL OR review_content IS NULL OR img_link IS NULL OR product_link IS NULL OR rating IS NULL OR rating_count IS NULL

```

Consulta para encontrar duplicados, foram encontrados 96 os dados estão totalmente iguais, irei deixei somente os unicos, 4 duplicados estavam preenchidos nulos na coluna rating_count, excluindo essas linhas com nulos e mantendo somente a coluna que está preenchida.

```jsx
SELECT product_id,
COUNT(*)
FROM `amazonsales-425320.origem.produto`

GROUP BY product_id 
having count(*) >1
```

Retornando apenas ids unicos

```jsx
tabela AS (
  SELECT 
    product_id, product_name, category, discounted_price, actual_price, discount_percentage, about_product,
    ROW_NUMBER() OVER (PARTITION BY product_id ORDER BY product_id) AS id_produto
  FROM `amazonsales-425320.origem.produto`
)

SELECT 
  product_id, product_name, category, discounted_price, actual_price, discount_percentage, about_product
FROM tabela
WHERE id_produto = 1

```

Foram encontrados 92 valores duplicados

```jsx
SELECT product_id,

COUNT (*),

 FROM `amazonsales-425320.origem.review` 
 
 GROUP BY product_id
 having count(*) >1
```

Consulta para identificar valores duplicados, foram encontrados 92 e após retornar apenas os valores únicos.

```jsx
WITH duplicados AS (

SELECT product_id,

COUNT (*),

 FROM `amazonsales-425320.origem.review` 
 
 GROUP BY product_id
 having count(*) >1),

 tabela AS (
SELECT product_id,user_id, user_name,review_id, review_title, review_content, img_link, product_link, rating, rating_count,
ROW_NUMBER() OVER (PARTITION BY product_id ORDER BY product_id) AS id_produto
FROM `amazonsales-425320.origem.review` 
 )

 SELECT product_id, user_id, user_name,review_id, review_title, review_content, img_link, product_link,  rating, rating_count
 FROM tabela
 WHERE id_produto = 1
```

Consulta para alterar dados disprepantes  nas 2 tabelas

```jsx
CREATE OR REPLACE TABLE `amazonsales-425320.tratados.review` AS
WITH temporariaa AS(
SELECT *,
REGEXP_REPLACE(user_name, r'[^a-zA-Z0-9 ]', '') AS user_name_clean,
REGEXP_REPLACE(review_title, r'[^a-zA-Z0-9 ]', '') AS review_title_clean,
REGEXP_REPLACE(review_content, r'[^a-zA-Z0-9 ]', '') AS review_clean

 FROM `amazonsales-425320.tratados.review`),
 
 novo AS (
  SELECT * EXCEPT(user_name,review_title,review_content),
  FROM temporariaa
 )

 SELECT * FROM novo;

CREATE OR REPLACE TABLE `amazonsales-425320.tratados.produto` AS
WITH temporaria AS (
SELECT *,
REGEXP_REPLACE(product_name, r'[^a-zA-Z0-9 ]', '') AS product_name_clean,
REGEXP_REPLACE(category, r'[^a-zA-Z0-9 ]', ' ') AS category_clean,
REGEXP_REPLACE(about_product, r'[^a-zA-Z0-9 ]', '') AS about_product_clean

 FROM `amazonsales-425320.tratados.produto`),

 teste AS (SELECT * EXCEPT (product_name,category,about_product),

 FROM temporaria

 )

 SELECT *, FROM teste;
```

Consulta para alterar a coluna rating para FLOAT, estava como STRING.

```jsx
CREATE OR REPLACE TABLE `amazonsales-425320.tratados.review`
AS
SELECT*,
 SAFE_CAST (rating AS FLOAT64) AS new_rating

 FROM `amazonsales-425320.tratados.review`;
```

Consulta de medidas de tendência central. 

```jsx

SELECT 
MIN(rating_count) AS min_rating, --2--
MAX(rating_count) AS max_rating, --426973--
AVG(rating_count) AS avg_rating, --17644.49--
APPROX_QUANTILES(rating_count, 100)[SAFE_ORDINAL(50)] AS mediana_rating, --4584--
STDDEV(rating_count) AS desvio_rating, --42145.64--

MIN(new_rating) AS min_rating, --2--
MAX(new_rating) AS max_rating, --5--
AVG(new_rating) AS avg_rating, --3,98--
APPROX_QUANTILES(new_rating, 100)[SAFE_ORDINAL(50)] AS mediana_rating, --4--
STDDEV(new_rating) AS desvio_rating --0,25--
 FROM `amazonsales-425320.tratados.review` 

SELECT 
MIN(discounted_price) AS min_price, --39,00--
AVG(discounted_price) AS avg_price, --3304.80--
APPROX_QUANTILES(discounted_price, 100)[SAFE_ORDINAL(50)] AS mediana_price,--848.99--
STDDEV(discounted_price) AS desvio_price,--7173.98--
MAX(discounted_price) AS max_price, --77990.0--

MIN(actual_price) AS min_actual,--39.0--
AVG(actual_price) AS avg_actual, --5690.51--
APPROX_QUANTILES(actual_price, 100)[SAFE_ORDINAL(50)] AS mediana_actual,--1699.0--
STDDEV(discounted_price) AS desvio_actual,--7173.98--
MAX(actual_price) AS max_actual, --139900.0--

MIN(discount_percentage) AS min_discount, --0.0--
AVG(discount_percentage) AS avg_descount, --0.466--
APPROX_QUANTILES(discount_percentage, 100)[SAFE_ORDINAL(50)] AS mediana_discount, --0.48--
STDDEV(discount_percentage) AS desvio_discount, --0.21--
MAX(discount_percentage) AS max_discount --0.94--

 FROM `amazonsales-425320.tratados.produto` 
```

 Consulta para criar espaço entre os nomes das categorias, estavam escritas juntas.

```jsx
CREATE OR REPLACE TABLE `amazonsales-425320.tratados.produto` 
AS 
SELECT
 *,
SPLIT(category_clean, ' ')[OFFSET(0)] AS categoria
FROM `amazonsales-425320.tratados.produto`;

```

Consulta para pegar apenas o primeiro nome de cada categoria.

```jsx
SELECT
  *,
  SPLIT(category_clean, ' ')[SAFE_OFFSET(0)] AS nova_categoria
FROM
  `amazonsales-425320.tratados.produto`;
```

Consulta para efetuar a união das tabelas.

```jsx
SELECT 
t.product_id,t.discounted_price,t.actual_price,t.discount_percentage,t.product_name_clean,t.about_product_clean,t.category, r.user_id, r.review_id,r.rating,r.rating_count,r.user_name_clean,r.review_title_clean,r.review_clean

 FROM `amazonsales-425320.tratados.produto`  AS t full join 
 `amazonsales-425320.tratados.review`  AS r ON r.product_id = t.product_id
```

Consulta para alterar o nome das categorias e colocar  CAR, HEALTH, MUSI E TOYS na mesma categoria.

```jsx
CREATE OR REPLACE TABLE `amazonsales-425320.tratados.tabela_unificada` AS
SELECT  * EXCEPT (category),
  CASE 
  WHEN category = 'Car' THEN 'Outros segmentos' 
  WHEN category = 'Health' THEN 'Outros segmentos'
  WHEN category = 'HomeImprovement' THEN 'Outros segmentos'
  WHEN category = 'MusicalInstruments' THEN 'Outros segmentos'
  WHEN category = 'Toys' THEN 'Outros segmentos'
  WHEN category = 'Home' THEN 'Home&Kitchen'
  WHEN category = 'OfficeProducts' THEN 'Office Products'
  ELSE category

  END AS nova_categoria

 FROM `amazonsales-425320.tratados.tabela_unificada`

```

Consulta para  QUARTIL e criação de nova varíavel, categoria criada com base nas notas de NEW_RATING.

```jsx
CREATE OR REPLACE TABLE  `amazonsales-425320.tratados.tabela_unificada` 
AS
WITH temporaria AS (
    SELECT *,
        NTILE(4) OVER (ORDER BY discount_percentage) AS quartil_percentage,
        NTILE(4) OVER (ORDER BY new_rating) AS quartil_rating
    FROM `amazonsales-425320.tratados.tabela_unificada`
),
teste AS (
 SELECT 
 *,
 CASE 
 WHEN new_rating BETWEEN 0 AND 1 THEN 'Insatisfeito'
WHEN new_rating >=1 AND new_rating <2 THEN 'Pouco Satisfeito'
 WHEN new_rating >=2 AND new_rating <3 THEN  'Neutro'
 WHEN new_rating >=3 AND new_rating <4 THEN  'Satisfeito'
 WHEN new_rating >=4 AND new_rating <5 THEN  'Muito Satisfeito'   
 END AS grau_satisfacao
 FROM temporaria
)
SELECT * FROM teste;

```

**União das tabelas:**
As tabelas foram unificadas em uma única tabela denominada **"TABELA_UNIFICADA"**. Após a unificação, a tabela foi carregada no Google Colab, onde foram realizadas análises de sentimento e regressão linear.