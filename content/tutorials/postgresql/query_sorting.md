+++
title = 'Query_sorting'
date = 2023-09-03T22:06:03-03:00
draft = true
+++

# Técnicas de Sort

## Introdução

Os métodos de sort são operações centrais na execução de queries e em sistemas de bancos de dados, com o proposito de buscar por dados específicos, ordenar ou agregar dados que serão retornados.

## Disk Merge Sort

Quando estamos utilizando uma base muito grande e não possuímos RAM o suficiente para executar a query precisamos utilizar o método de busca [[Sorting#Merge Sort - not finished |merge sort]], pois os dados que não cabem na memória são salvos em disco. Ele tem a tendencia a set um método muito lendo devido à natureza da memoria em disco e suas limitações de hardware.

## Quick Sort

Quando se possui memória suficiente para realizar a query anterior o _psql_ utilize o [[Sorting|quick sort]] para realizar a operação de busca e agregação. O quick sort utilizado aqui é um pouco diferente da versão mais generalista, sendo adaptado para a linguagem dos postgres. É um método muito mais rápido que o merge sort.

## Heap Sort

Para o postgresql quando temos uma limitação da quantidade de rows a serem retornadas o método escolhido para ordenação é o heap sort, em questão de performance temos algo similar ao quick sort, a grande diferença é em relação ao consumo de memória, que é consideravelmente mais baixo.

## Index Sort

Como os índices já estão ordenados esse tipo de ordenação tende a set MUITO rápido, porem vem com a contra partida de ter um consumo de memória grande, sendo necessário avaliar muito bem a necessidade da criação de um índice.
