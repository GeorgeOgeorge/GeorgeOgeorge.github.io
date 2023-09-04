+++
title = 'Jsonb Summary'
date = 2023-09-03T21:34:08-03:00
draft = false
+++

# Explorando o Poder das Colunas JSONB no PostgreSQL

Nos ecossistemas de dados modernos, onde a flexibilidade e a escalabilidade são cruciais, o PostgreSQL se destaca como um dos principais sistemas de gerenciamento de bancos de dados relacionais. Uma das características mais poderosas e versáteis que o PostgreSQL oferece é a coluna JSONB.

Esta coluna permite armazenar dados em formato JSON binário, que na prática se manifesta em algumas maneiras:

- objetos json simples:

``` json
{"nome": "João", "idade": 30}
```

- arrays:

``` json
["foo", "foo1", "foo2"]
```

- arrays de objetos:

``` json
[{"nome": "João", "idade": 30}, {...}, ...]
```

- objetos json complexos:

``` json
{"nome": "João", "idade": 30, "amigos": [{...}, {...}]}
```

O JSONB combina a estrutura de um banco de dados relacional com a flexibilidade de um armazenamento NoSQL.

# Benefícios e cuidados necessários com JSONB

As colunas JSONB no PostgreSQL oferecem uma série de benefícios, permitindo maior flexibilidade no armazenamento e consulta de dados. No entanto, também é importante considerar alguns cuidados ao utilizá-las, a fim de evitar problemas de desempenho, complexidade e integridade dos dados.

Ao utilizar colunas JSONB, é fundamental encontrar um equilíbrio entre a flexibilidade e a estruturação dos dados. Avalie cuidadosamente os benefícios e cuidados que serão mencionados para garantir que a utilização das colunas JSONB seja eficaz e atenda às suas necessidades.

## Benefícios

### Flexibilidade de Esquema

As colunas JSONB permitem armazenar dados com esquemas flexíveis e dinâmicos. Isso é especialmente útil quando se lida com dados que podem variar em estrutura, como metadados ou preferências do usuário.

### Consulta Flexível

Com operadores e funções específicas, é possível executar consultas complexas em dados JSONB. Você pode extrair, modificar e filtrar dados de maneira eficiente, sem a necessidade de modificar a estrutura da tabela.

### Suporte a Dados Aninhados

As colunas JSONB podem conter objetos aninhados e arrays, permitindo modelar dados hierárquicos sem a necessidade de criar várias tabelas relacionais.

### Armazenamento Compacto

O formato JSONB armazena dados de forma otimizada, economizando espaço em disco em comparação com o formato json.

## Cuidados a Tomar

### Desempenho

Consultas complexas em colunas JSONB podem set mais lentas em comparação com tabelas relacionais tradicionais. O uso indevido de funções de desnormalização e consulta pode impactar negativamente o desempenho.

### Integridade dos Dados

Como o PostgreSQL não impõe um esquema rígido em colunas JSONB, você deve set responsável pela integridade dos dados. Certifique-se de validar e garantir a consistência dos dados inseridos.

### Indexação Adequada

Indexar colunas JSONB pode melhorar o desempenho das consultas, mas é importante escolher cuidadosamente quais campos indexar para evitar indexação excessiva e ineficiente.

### Atualizações e Migrações

Modificar dados aninhados em uma coluna JSONB pode set complexo. Mudanças frequentes na estrutura dos dados podem exigir atualizações complicadas em consultas e migrações.

### Compatibilidade com Ferramentas

Algumas ferramentas de análise e visualização podem não lidar bem com dados armazenados em colunas JSONB. Verifique a compatibilidade das ferramentas que você utilize.

### Documentação Adequada

Devido à flexibilidade de esquema, é importante documentar claramente a estrutura esperada dos dados em colunas JSONB, para facilitar o desenvolvimento contínuo e a colaboração da equipe.

# Utilização do JSONB

Agora que estabelecemos uma compreensão básica sobre o que é JSONB em tabelas PostgreSQL, iremos compreender o processo de manipulação de dados. Nesta seção, vamos explorar os recursos que o PostgreSQL oferece para trabalhar com dados JSONB. Desde a inserção e atualização de informações até a extração de valores específicos, você descobrirá como tirar o máximo proveito dessa poderosa funcionalidade. Veremos que a criação e inserção dos dados são muito similares às colunas comuns do psql, não existindo distinção de utilização para a deleção de registros. Tendo isso em mente teremos um maior foco no acesso e atualização dos dados registrados na tabela.

## Criação de Coluna JSONB

Seguem alguns exemplos de como criar uma coluna JSONB em uma tabela PostgreSQL.

- PSQL Cli:

``` sql
CREATE TABLE exemplo (
	id serial PRIMARY KEY,
	dados JSONB
);
```

- Python sqlAlchemy Migration:

``` python
from sqlalchemy.dialects import postgresql
import sqlalchemy as sa
from alembic import op

op.add_column(
	'nome_tabela',
	sa.Column(
		'nome_coluna',
		postgresql.JSONB(astext_type=sa.Text()),
		server_default=sa.text('jsonb_build_array()' ou 'jsonb_build_object()'),
		nullable=False
	)
)
```

## Inserção de Dados

Seguem alguns exemplos de como inserir dados em uma coluna JSONB no PostgreSQL e a partir de classes modelos do SqlAlchemy.

É importante ressaltar a necessidade de validar a estrutura dos dados que serão inseridos na coluna JSONB para garantir a integridade das informações cadastradas. Isso significa verificar se os dados estão no formato correto e se não contém nenhuma informação inválida. Se os dados não forem validados, podem causar errors no banco de dados ou fazer com que os dados estejam incorretos, acarretando em problemas no futuro para acesso e processamento das informações contidas na coluna.

Para inserir dados na coluna JSONB, você pode usar a seguinte sintaxe:

- PSQL Cli:

>Até o memento atual ainda não temos uma ferramenta de validação de schema nativa para psql, então tenha bastante cuidado ao inserir o valor na coluna.

``` sql
INSERT INTO exemplo (dados)VALUES ('{"nome": "João", "idade": 30}'::jsonb);
```

- Python sqlAlchemy Migration:

>Para a validação de schemas no python possuímos algumas bibliotecas como [Pydantic](https://docs.pydantic.dev/latest/) e [Marshmallow](https://pypi.org/project/marshmallow/), é fortemente recomendado a utilização delas para garantir a consistência dos dados que serão salvos no banco de dados.

``` python

if not db.session.is_active:
	db.session.begin()

try:
	new_instance = Model(dados={"nome": "João", "idade": 30})
	db.session.add(new_instance)
	db.session.commit()
except Exception:
	db.session.rollback()finally:
	db.session.close()
```

## Acesso aos Dados

Existem diversas maneiras para acessar os valores de uma coluna JSONB no PostgreSQL. Nesta seção do documento, abordaremos algumas delas, desde as mais simples até as mais avançadas.

### Operadores de Acesso

O PostgreSQL fornece alguns operadores para acessar os valores de uma coluna JSONB de maneira simples e ágil, os operadores a seguir são recomendados para operações simples em objetos que possuem poucos níveis e que não possuam arrays.

#### Acesso posicional (`->` e `->>`)

O operador de acesso posicional para JSONB tem como função acessar um objeto ou valor contido dentro de um array JSONB, ele funciona como o acesso via índice que já estamos acostumados. Para o PSQL possuímos uma particularidade pois assim como no python podemos acessar o valor de maneira crescente ou decrescente.

- PSQL Cli:

``` sql
-- acessa primeiro valor
select '[{"nome": "João1", "idade": 31}, {"nome": "João2", "idade": 32}]'::jsonb -> 0;
-- acessa último valor
select '[{"nome": "João1", "idade": 31}, {"nome": "João2", "idade": 32}]'::jsonb ->> -1;
```

- SqlAlchemy Python:

``` python
# acessa primeiro valor
Model.query.with_entities(Model.dados.op("->")(0))
# acessa último valor
Model.query.with_entities(Model.dados.op("->>")(-1))
```

#### Acesso nomeado (`->` e `->>`)

Em um Objeto JSONB quando queremos acessar o valor de uma chave podemos utilizar o operador de acesso nomeado com o nome da chave à sua direita.

- PSQL Cli:

``` sql
select '{"nome": "João1", "idade": 31}'::jsonb -> 'nome';
```

- SqlAlchemy Python:

``` python
# acessando ->
Model.query.with_entities(Model.dados['nome'])
# acessando ->>
Model.query.with_entities(Model.dados['nome'].astext)
```

> [!CAUTION]
> Note que o que diferencia o acesso posicional do nomeado é o tipo do valor à direita do operador. Também vale destacar que caso utilize o operador `->` e `#>` seu retorno será um outro objeto JSONB, porém podemos utilizar `->>` e `#>>` para que seu retorno seja do tipo texto.

#### Acesso via path/caminho (`#>` e `#>>`)

Em algumas ocasiões teremos que fazer um acesso que não será direto, talvez um valor que esteja em um objeto aninhado, para esse caso queremos evitar a repetição de código, logo o operador de path se mostra muitíssimo útil pois além de chaves também podemos acessar por índice de um array que exista no objeto.

- PSQL Cli:

``` sql
-- acessando duas chaves
select '{"nome": "João", "idade": 30, "dados": {"cpf": 1, "amigos": 1000}}'::jsonb #> '{dados,cpf}';
-- acessando chave e índice
select '{"nome": "João", "idade": 30, "dados": {"cpf": 1, "amigos":['ana', 'caio']}}'::jsonb #> '{dados,amigos,0}';
```

- SqlAlchemy Python:

``` python
# acessando #>
Model.query.with_entities(Model.dados.op('#>')('{dados,cpf}'))
# acessando #>>
Model.query.with_entities(Model.dados.op('#>>')('{dados,amigos,0}'))
```

### Operadores de Lógica e Manipulação

Em algumas situações será necessário validar se valores ou chaves existem dentro de um objeto, deletar uma chave ou concatenar um valor.

> [!TIP]
> Um ponto muito positivo dos operadores nativos é que else podem set utilizados dentro das cláusulas _where_.

#### Chave/valor Em objeto (@> e <@)

Esse operador é utilizado em situações que queremos saber se uma chave específica possui um valor. O `@>` faz a validação se o objeto da esquerda possui a chave e valor da esquerda. Já o operador `<@` válida se _**algum**_ dos objetos da direita está contido no objeto da esquerda.

- PSQL Cli:

``` sql
-- utilizando @>
select '{"nome": "João", "idade": 31}'::jsonb @> '{'nome': João}'::jsonb;
-- utilizando <@
select '{"nome": "João"}'::jsonb <@ {"nome": "João", "idade": 31}::jsonb;
```

- SqlAlchemy Python:

``` python
# utilizando @>
Model.query.with_entities(Model.dados.op('@>')({'nome': João}))
# utilizando <@
Model.query.with_entities(Model.dados.op('<@')({"nome": "João", "idade": 31}))
```

#### Chave(s) Em objeto (`?` e `?|` e `?&`)

Em casos que seja necessário validar a existência de uma `?`, alguma `?|` ou todas `?&` chaves dentro de um objeto fazemos o uso desses operadores.

- PSQL Cli:

``` sql
-- procura por uma chave
select '{"nome": "João", "idade": 31}'::jsonb ? 'nome';
-- valida se qualquer chave existe
select '{"nome": "João"}'::jsonb ?| array['nome', 'carro', 'peixe'];
-- valida se todas as chaves existem
select '{"nome": "João", "idade": 31}'::jsonb ?& array['nome', 'idade'];
```

- SqlAlchemy Python:

``` python
# procura por uma chave
Model.query.with_entities(Model.dados.op('?')('nome'))
# valida se qualquer chave existe
Model.query.with_entities(Model.dados.op('?|')(['nome', 'carro', 'peixe']))
# valida se todas as chaves existem
Model.query.with_entities(Model.dados.op('?&')(['nome', 'carro', 'peixe']))
```

#### Concatenar valor (`||`)

Utilizamos esse operador para realizar operações onde se mostra necessário adicionar uma valor a uma chave e valor a um objeto ou um novo valor para um array.

>[!CAUTION]
>quando estamos adicionando uma nova chave para um objeto, caso a chave já exista o valor salvo será substituído pelo novo valor.

- PSQL Cli:

``` sql
-- adicionar nova chave
select '{"nome": "João", "idade": 31}'::jsonb || '{"amigos": 10}'::jsonb;
-- adiciona novos valores à array
select '[1,2,3,4]'::jsonb || '[5,6]'::jsonb;
```

- SqlAlchemy Python:

``` python
# adicionar nova chave
Model.query.with_entities(Model.dados.op('||')({"amigos": 10}))
# adiciona novos valores à array
Model.query.with_entities(Model.dados.op('||')([5,6])
```

#### Deletando Valores (`-` e `#-`)

Quando é necessário deletar valores de um objeto podemos deletar nominalmente, posicionamento ou via caminho/path.

>[!NOTE]
>Assim como o operador `->`, o que diferencia a deleção nominal para índice é o valor passado na direita do operador, caso seja texto fará a deleção nominal, caso seja inteiro fará a deleção via índice.

- PSQL Cli:

``` sql
-- deleta via chave
select '{"nome": "João", "idade": 31}'::jsonb - 'nome';
-- deleta via indice
select '[1,2,3,4]'::jsonb - 1;
select '[1,2,3,4]'::jsonb - -1;
-- deleta via caminho/path
select '{"nome": "João", "idade": 31, "amigos": ["ana", "pedro"]}'::jsonb #- '{amigos,1}';
```

- SqlAlchemy Python:

``` python
# deleta via chave
Model.query.with_entities(Model.dados.op('-')("nome"))
# deleta via indice
Model.query.with_entities(Model.dados.op('-')(1))
# deleta via caminho/path
Model.query.with_entities(Model.dados.op('-')("{amigos,1}"))
```

## Funções de manipulação

Em alguns casos os operadores nativos do PSQL não serão suficientes para alguns objetivos, com isso temos algumas funções que podem set utilizadas para alcançar esses objetivos.

### jsonb_array_elements

A função `jsonb_array_elements()`, que é usada para expandir um array JSONB em linhas individuais. Essa função é útil quando você deseja tratar os elementos de um array JSONB como registros separados, facilitando a consulta e análise de dados JSONB.

- PSQL Cli:

``` sql
select jsonb_array_elements('{"nome": "João", "amigos": [{"nome": "ana"}, {"nome": "pedro"}]}'::jsonb -> 'amigos');
```

- SqlAlchemy Python:

``` python
from sqlalchemy import func

Model.query.with_entities(func.jsonb_array_elements(Model.data['amigos']))
```

### jsonb_build_array

A função `jsonb_build_array()` é usada para construir um array JSONB a partir de valores fornecidos separados por vírgula. Ela é particularmente útil quando você deseja criar um array JSONB contendo valores específicos ou resultados provenientes de colunas da tabela ou de subconsultas.

- PSQL Cli:

``` sql
select jsonb_build_array(1, 'a', true, row(2, 'b', false));
```

- SqlAlchemy Python:

``` python
from sqlalchemy import func

Model.query.with_entities(func.jsonb_build_array(1, 'a', true))
```

### jsonb_build_object

A função `jsonb_build_object()` permite criar objetos JSONB de forma dinâmica. Você pode fornecer pares chave-valor como argumentos para a função, e ela retornará um objeto JSONB contendo esses pares.

- PSQL Cli:

``` sql
-- simples
select jsonb_build_object('nome', 'joao', 'idade', 30);
-- com array
select jsonb_build_object('nome', 'joao', 'amigos', jsonb_build_array('ana', 'pedro'));
-- com objeto
select jsonb_build_object('nome', 'joao', 'familia', jsonb_build_object('mae', 'ana', 'pai', 'pedro'));
```

- SqlAlchemy Python:

``` python
from sqlalchemy import func

# simpels
Model.query.with_entities(func.jsonb_build_object('nome', 'joao', 'idade', 30))
# com array
Model.query.with_entities(func.jsonb_build_object('nome', 'joao', 'amigos', func.jsonb_build_array('ana', 'pedro')))
# com objeto
Model.query.with_entities(func.jsonb_build_object('nome', 'joao', 'familia', func.jsonb_build_object('mae', 'ana', 'pai', 'pedro')))
```

### jsonb_insert

Muito parecido com o operador de concatenação a função `jsonb_insert()` é usada para inserir um novo valor em uma estrutura JSONB, porém nos dá a opção de definir um caminho para alteração. A função recebe três parâmetros:

- O valor JSONB em que o novo valor será inserido.
- Um array de caminhos que especificam a localização do novo valor. O primeiro caminho no array deve apontar para o elemento pai do novo valor.
- O novo valor a set inserido.
- booleano indicando se deve adicionar após o local indicado

``` sql
-- simples
select jsonb_insert('{"nome": "joao"', '{idade}', '30');
-- com path sem adicionar após 0
select jsonb_insert('{"nome": "joao", "amigos": ["ana", "pedro"]}', '{amigos,
0}', '"paulo"');
-- com path e adiciona após 0
select jsonb_insert('{"nome": "joao", "amigos": ["ana", "pedro"]}', '{amigos,
0}', '"paulo"', true);
```

> [!NOTE]
>- Note que para adicionar um inteiro deve-se utsar aspas simples `30` e para adiconar um texto deve-se utilizar aspas com aspas duplas `"pedro"`.
>- Para fazer a alteração via python recomendo utilizar dicionário pois é mais fácil e rápido.
>- A query retorna error caso esteja substituindo uma chave já existente.

### jsonb_array_lenght

A função `jsonb_array_lenght()` é usada para calcular o número de elementos em um array JSONB. Ela pode set útil para consultas que envolvem contagens de dados e etc. Note que ela conta apenas a quantidade de elementos na primeira camada do array.

- PSQL Cli:

``` sql
select jsonb_array_length('["bruno", 1, "pedro", [3, 4]]');
```

- SqlAlchemy Python:

``` python
from sqlalchemy import func

Model.query.with_entities(func.jsonb_array_length(Model.data))
```

### jsonb_path_match

A função `jsonb_path_match()` é usada para verificar se um determinado valor JSONB corresponde a um determinado caminho/paths. A função retorna um valor booleano que indica se o valor JSONB corresponde ao _path_, que por sua vez é um conjunto de chaves que especificam a localização do valor a ser verificado. Recomendo seu uso para a verificação de valores em JSONBs complexos como o de configuração de regras, kpis, gráficos, etc.

Se utilizado corretamente os _path_queries_ serão seus maiores amigos ao manipular JSONB pois permitirão que você faça a validação de objetos complexos diretamente na cláusula where da consulta, sem a necessidade de utilizar funções de agregação para abrir arrays de objetos, deixando seu código mais direto e limpo.

A função `jsonb_path_match()` recebe dois parâmetros:

- O valor JSONB a ser verificado.
- O caminho que deve ser verificado.

> [!NOTE]
> Não entrarei em detalhes quanto à construção de json_paths nesse documento, recomendo fortemente o estudo do uso de json_path_queries no [jsonpath.com](https://jsonpath.com/), nele você encontrará exemplos de manipulação.

- PSQL Cli:

``` sql
select t.id from "table" t
where jsonb_path_match(
	t.details::jsonb,
	'exists(
		$.families[*].parents[*].kids[*]
		? (
			(@.name == "tom" && @.age == 10)
			|| (@.name == "clara" && @.age == 20)
		)
	)'
);
```

O path acima é construído em algumas partes irei explicar cada uma separadamente:

1. A palavra chave exists funciona para retornar true ou false a depender da condicional utilizada dentro dele.
2. Como caminho estamos olhando para as condições de um grupo dentro de uma regra: groups -> groups -> rules, nesse caso estamos utilizando o * que faz o unpacking de arrays similar à função [[Colunas JsonB#Funções de manipulação#jsonb_array_elements|jsonb_array_elements]].
3. O caractere @ representa o objeto em si, nesse caso utilizamos o ? para fazer uma condicional.
4. A condicional verifica se a categoria da regra é de cliente e seu campo de condição é de jornada ou se é de fase de jornada.

- SqlAlchemy Python:

``` python
from sqlalchemy import func, cast

Model.query
.with_entities(Model.id, Model.params)
.filter(func.jsonb_path_match(
	cast(Model.params, JSONB),
	'exists($.families[*].parents[*].kids[*] ? ((@.name == "tom" && @.age == 10) || (@.name == "clara" && @.age == 20)))'
)
```

### jsonb_path_query e jsonb_path_query_array

As funções `jsonb_path_query()` e `jsonb_path_query_array()` são similares à [[Colunas JsonB#jsonb_path_match |jsonb_path_match]] pois também utilizam um path para verificar uma condicional, porém possuem retornos diferentes, o `jsonb_path_query` retorna um conjunto de _rows_, quando o `jsonb_path_query_array` retorna um único array com todos os resultados.

- PSQL Cli:

``` sql
-- retorna rows
select r.id from "banana" where jsonb_path_query(r.params::jsonb, '$.*.*.*.rule.field') ? 'teste'
-- retorna array
select r.id from "banana" where jsonb_path_query(r.params::jsonb, '$.*.*.*.rule.field') ? 'teste'
```

- SqlAlchemy Python:

``` python
from sqlalchemy import func, cast

# retorna rows
Model.query.with_entities(Model.id)
.filter(func.jsonb_path_query(cast(Model.params, JSONB), '$.*.*.*.rule.field').op('?')("teste"))
# retorna array
Model.query.with_entities(Model.id)
.filter(func.jsonb_path_query_array(cast(Model.params, JSONB), '$.*.*.*.rule.field').op('?')("teste"))
```
