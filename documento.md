%md

# Analise de Dados em SQL

### Pratica em analise de dados em sql baseada no livro "SQL FOR DATA ANALISYS"
_obs: Há arquivos com script para criar todas as tabelas e script com código para criar e também o código da analise, mas da para acompanhar tudo aqui_

### Preparando os dados para a analise: 
Então, a maioria do tempo de trabalho para um analista de dados é tratando os dados, preparando para a analise, conhecendo os dados. E sobre isso veremos abaixo

Para a preparação de dados, há muitas tecnicas para preparação dos dados, e nesse documento iremos ver outras tecnicas naão abordada anteriomente

A tabela abaixo conseguimos ver dados de alguns clientes (_obs: não estou mostrando todos os dados, há 54 linhas no dataset que iremos analisar_)

#### Tabela:
| customer_id | name       | registration_date | age |
|------------:|------------|-------------------|----:|
| 16 | Paula      | 12/04/2023 | 22 |
| 17 | Quentin    | 15/04/2023 | 24 |
| 18 | Rafael     | 18/04/2023 | 25 |
| 19 | Sofia      | 20/04/2023 | 26 |
| 20 | Tiago      | 22/04/2023 | 28 |
| 21 | Ursula     | 25/04/2023 | 28 |
| 22 | Victor     | 01/05/2023 | 30 |
| 23 | Wagner     | 03/05/2023 | 31 |
| 24 | Xavier     | 05/05/2023 | 32 |
| 25 | Yasmin     | 07/05/2023 | 33 |
| 26 | Zeca       | 10/05/2023 | 35 |
| 27 | Bruno      | 12/05/2023 | 36 |

Aqui conseguimos tira uns insghts, quais idades se repete? qual a faixa etária dos meus clientes?


#### Código:
```
SELECT age, COUNT(customer_id) as customers
FROM customers
GROUP BY age 
;
```

#### Resultado:
| age | customers |
|----:|----------:|
| 22 | 1 |
| 23 | 1 |
| 24 | 2 |
| 25 | 2 |
| 26 | 2 |
| 27 | 2 |
| 28 | 4 |
| 29 | 2 |
| 30 | 2 |
| 31 | 2 |
| 32 | 2 |
| 33 | 3 |
| 34 | 2 |

acabamos de fazer uma distribuição com as idades e percebemos as idades são bem porpocionais, não há uma idade onde a maioria tem, a que tem mais quantidade é 28 anos

porém podemos ver a distribuição de clientes ultilizando outra forma, ou melhor, ultilizando outra tabela

#### tabela:
| order_id | customer_id | order_date | amount |
|---------:|------------:|------------|-------:|
| 8  | 16 | 01/02/2024 | 45.0 |
| 9  | 17 | 02/02/2024 | 20.0 |
| 10 | 17 | 10/02/2024 | 35.0 |
| 11 | 18 | 03/02/2024 | 15.0 |
| 12 | 18 | 05/02/2024 | 60.0 |
| 13 | 18 | 08/02/2024 | 25.0 |
| 14 | 19 | 04/02/2024 | 40.0 |
| 15 | 20 | 06/02/2024 | 30.0 |
| 16 | 20 | 12/02/2024 | 55.0 |
| 17 | 21 | 07/02/2024 | 22.0 |
| 18 | 22 | 08/02/2024 | 18.0 |
| 19 | 22 | 11/02/2024 | 27.0 |

Ao realizar este código a baixo, irá criar um categoria com a quantidade de pedidos de 1 a 3
```
SELECT customer_id, COUNT(order_id) as orders
	FROM orders
	GROUP BY customer_id
	;
```
Agora vamos ver como ficou distribuido, a quantidade de clientes
#### codigo:
```
SELECT 
	orders, 
	COUNT(*) as num_custormers
FROM(
  SELECT customer_id, COUNT(order_id) as orders
	FROM orders
	GROUP BY customer_id
) a
GROUP BY 1
;
```
#### resultado:
| orders | num_customers |
|-------:|--------------:|
| 1 | 25 |
| 2 | 19 |
| 3 | 11 |

outro exemplo de categoria e agrupamento é usando CASE, que seria condicional no sql

Exemplo:

####codigo:
```
SELECT
    CASE
        WHEN amount <= 50 THEN '-50_agrupado'
        WHEN amount >= 55 THEN '+55_agrupado'
        ELSE 'sem agrupamento'
    END AS amount_bin,
    CASE
        WHEN amount <= 50 THEN 'MENOS'
        WHEN amount >= 55 THEN 'MAIS'
        ELSE 'SEM_CATEGORIA'
    END AS amount_category,
    COUNT( DISTINCT customer_id) AS customers
FROM 
	orders
GROUP BY 
	amount_bin, amount_category;
;
```
####resultado:
| amount_bin       | amount_category | customers |
|------------------|-----------------|-----------|
| +55_agrupado     | MAIS            | 15        |
| -50_agrupado     | MENOS           | 45        |
| sem_agrupamento  | SEM_CATEGORIA   | 5         |

Aqui conseguimos ver, caso o amount formenor ou igual a 50 sera '-50_agrupado' e 'MENOS', ou seja, criei uma condição que agrupou e categorizou os dados
