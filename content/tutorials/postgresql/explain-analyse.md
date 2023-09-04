+++
title = 'Explain Analyse'
date = 2023-09-03T21:55:27-03:00
draft = true
+++

# Explain Analyse

## Introdução

O explain é uma ferramenta presente no PSQL que possibilita a compreensão dos processos de execução de uma query, listando e explicando seus:

- Custos de execução
- Tempo de execução
- Tempo de planejamento
- Técnicas de execução

## Como Utilizar O Explain Analyse

Para utilizar o explain você deve coloca-lo antes de alguma query _psql_ para que ele seja executado.

```sql
EXPLAIN <ANALYSE> SELECT name FROM customers WHERE id = 1;
```

No caso acima setão retornados valores estimados para execução da query. Caso tenha interesse em saber os valores reais referentes a execução da query é necessario utilizar a palavra chave `ANALYSE` , com ela a query setá executada por completo e o explain trará os valores de sua execução.

> [!CAUTION]
> O `EXPLAIN ANALYSE` executa a query por completo então muito cuidado com queries que possuam _delete_ ou _update_, caso seja extritamente necessário executar tais queries não esqueça de utilizar um _roolback_.

## Entendendo o Output

Ao executar a query a seguir

```sql
EXPLAIN ANALYZE
SELECT *
from us_geonames
WHERE name = 'Tampa International Airport';
```

Temos o seguinte output

```sql
Gather
(cost=1000.00..30062.35 rows=7888 width=84)
(actual time=37.164..192.388 rows=1 loops=1)
Workers Planned: 3
Workers Launched: 3
-> Parallel Seq Scan on us_geonames
(cost=0.00..28273.55 rows=2545 width=84)
(actual time=93.085..130.232 rows=0 loops=4)
Filter: (name = 'Tampa International Airport'::text)
Rows Removed by Filter: 559758
Planning Time: 0.052 ms
Execution Time: 192.413 ms
(8 rows)
```

Para entender a estrutura apresentada devemos entender alguns conceitos:

1. Estrutura de árvore: A estrutura funciona por identações e ->, elas representam processos dentro de processos, no caso acima vemos que para o processo `GATHER` foi gerado um sub-processo para fazer um [_Parallel Sequential Scan_](https://www.postgresql.org/docs/current/parallel-plans.html#PARALLEL-SCANS) que por sua vez possui _tempo de execução_, _custo_ e _técnicas_. Vale lembrar que o output de cada filho é agregado para o pai.
2. Tempo de execução e planejamento: o tempo de execução de um nó é referente ao tempo necessario para que aquele nó seja executado e traga seu resultado. já o tempo de planejamento é o tempo necessario para o _PostgreSQL_ verificar as melhores tecnicas de ordenação ou filtro.
3. Estatisticas estimadas e de execução: Funciona como mencionado no [[Explain Analyse#Como Utilizar O Explain Analyse|Como Utilizar O Explain Analyse]]
4. Workers: Quando um processo é muito grande o _psql_ pode usar algumas técnicas para acelerar o processamento, uma dessas tecnicas são os _workers_. Digamos que temos uma tabela com 1.000.000 registros, ao ver isso o _psql_ divide os registros em tamanhos iguais e designa cada grupo de registro à um _worker_. Todos os _workers_ irão executar a mesma query ao mesmo tempo assim trocando tempo de execução por processamento ou memória.
