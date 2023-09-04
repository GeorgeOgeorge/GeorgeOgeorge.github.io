+++
title = 'Query Performance'
date = 2023-09-03T21:57:06-03:00
draft = true
+++

# Boas praticas para otimização

Na construção de _queries_ _psql_ sempre temos que ter em mente em primeiro lugar a performance da execução da _query_. Temos que ter bem definido:

- Quantidade de registros a serem retornados
- Quantidade de colunas em cada registro
- Quantidade de registros que serão filtrados
- Operações de agregações necessárias para o processamento dos dados

Assim que a _query_ esteja funcionando corretamente, devemos focar no máximo de melhoria da performance de suas operações.

## Quantidade de registros a serem retornados

Quando possuímos muitos registros a serem retornados por uma _query_, temos que ter em mente que esses valores serão processados por alguma backend, a depender do tamanho desse retorno sendo necessário fazer a paginação dos valores que serão retornados.

``` sql
SELECT column1, column2, ...
FROM table
ORDER BY column1
LIMIT <número de linhas por página>
OFFSET <número de linhas a serem ignoradas>
```

Algo que devemos sempre estar levando em consideração quando fazemos uma _querie_ é a quantidade de colunas que serão retornados pela consulta, trazendo apenas colunas que sejam realmente utilizadas pelo consumidor da _query_ o que diminui o tempo e peso da consulta.

> [!TIP]
> Também podemos utilizar a representação dos registros via chaves primarias e estrangeiras quando o contexto da utilização permitir.

## Quantidade de registros a serem filtrados

Para casos aonde estamos querendo filtrar um valor que existe em uma segunda tabela mas não queremos trazer todos os valores  para filtrar depois é possível fazer o join com o máximo possível de validações para trazer o minimo de registros em vez de fazer a consulta no _where_ após o join.

``` sql

select *
from studants s
join class c on
	s.id = c.studant_id
	and c.subject = 'math'
where s.id in (1,2,3,4);
```

No caso acima estaremos filtrando todos os valores diretamente no join o que diminui muito a quantidade de valores que serão filtrados no _where_.
