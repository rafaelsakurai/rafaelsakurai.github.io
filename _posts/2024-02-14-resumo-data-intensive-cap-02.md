---
layout: post
title: "Resumo do capítulo 2 do livro Designing Data-Intensive Applications"
date: 2024-02-14 22:40:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, modelo de dados, sql, nosql, mapreduce, grafo]
excerpt: Neste capítulo o autor aborda sobre como o dado é transformado entre as camadas da aplicação, os diversos tipos de banco de dados e linguagens para consultar os dados.
featured: false
hidden: true
---

No [capítulo 1](https://www.sakurai.dev.br/resumo-data-intensive-cap-01) foram abordados alguns requisitos não funcionais como disponibilidade, escalabilidade e manutenibilidade das aplicações, não apenas das que fazem uso intenso de dados, mas são conceitos importantes para qualquer tipo de aplicação.

## Capítulo 2 - Modelos de dados e linguagens de consulta

O modelo de dados é um dos pontos mais importantes do desenvolvimento de um software, as aplicações transferem dados entre as camadas da aplicação, escrevem dados no banco de dados, arquivos, memória, trafegam os dados de uma máquina para outra, de um servidor para o outro, etc.

O autor começa este capítulo comentando sobre como pensamos no modelo dos dados de acordo com o problema que a aplicação está tentando resolver. Cada profissional verá os dados de uma forma diferente, sendo como desenvolvedor que recebe dados informados pelos usuários e precisa passar esses dados entre as camadas da aplicação para persistir no banco de dados; seja como DBA que precisará administrar os bancos de dados para que possam suportar a demanda de leitura e escrita dos dados; seja como Data Scientist que irá manipular os dados para extrair insights; cada um tentará na sua camada abstrair a complexidade da camada anterior e irá manipular o dado com um determinado objetivo.

A maioria das aplicações constroem um modelo de dados em cima de outro modelo de dados, como uma forma de abstrair ou simplificar a camada anterior, imagine as camadas de uma aplicação:
1- Os dados são organizados em objetos e estruturas simulando o mundo real;
2- Depois os dados serão armazenados em tabelas de banco de dados, arquivos XML ou JSON, ou grafo;
3- O banco de dados vai internamente representar os dados em bytes na memória, disco ou rede, de tal forma que permita consultar, pesquisar, manipular e processar o dado de diversas formas;
4- Na camada mais baixa os dados são armazenados em circuitos elétricos, pulso de luz, campo magnético.

Dependendo do tipo da aplicação, há diversas outras camadas, como nas integrações entre aplicações dentro da mesma companhia ou entre companhias diferentes, em que cada um utiliza sua própria abstração do modelo de dados. Faça um zoom out na aplicação (ou aplicações) que geram os dados que você está trabalhando e veja por quantas camadas e quantas formas diversas o dado é manipulado.


### Modelo relacional versus Modelo documento

Aqui o autor faz um resumo da história começando com o modelo hierárquico (uma grande árvore), mas que não era bom para representar relacionamentos de muitos-para-muitos; depois aborda o domínio dos bancos de dados relacionais por aproximadamente 30 anos; e o surgimento dos banco de dados não relacionais (documento, colunar, grafo) como uma necessidade para obter mais escalabilidade, executar operações que não são suportadas em tabelas relacionais e flexibilizar o esquema das tabelas.

Lembrando que há uma troca (trade off), porque para ganhar algo as vezes precisa abrir mão de outra coisa, por exemplo usando um banco de dados em memória, pode ser super rápido para consultar, mas se desligar a máquina o banco de dados é apagado e precisa ser recriado; ou um banco de dados colunar permitirá particionar e consultar grandes volumes, mas provavelmente não vai fornecer o controle de transação.

Aqui vem um ponto interessante que é da **persistência poliglota**, na qual usamos mais de uma forma de persistência, podendo ser tanto banco de dados relacionais como não relacionais, imagine por exemplo uma aplicação que usa MySQL, MongoDB, Redis e BigQuery, são quatro bancos de dados diferentes que serão usados na mesma aplicação para objetivos específicos que cada um deles fornecerá. 

Dependendo da necessidade do negócio você escolherá um banco de dados que proporcionará o melhor resultado para atender o objetivo específico, não há necessidade de resolver todos os problemas usando o mesmo banco de dados, é como usar o martelo para apertar o parafuso, mas tenha cuidado, não vá achando que é só instalar o banco e disponibilizar em produção que está resolvido, cada banco de dados novo vem com sua complexidade para manter junto, então lembre do trade off entre não conseguir atender um objetivo com as ferramentas atuais e ter uma equipe capaz de lidar com uma nova ferramenta.

O autor contextualiza o uso do mapeamento objeto/relacional (ORM) para tratar a conversão entre os dados armazenados em tabelas relacionais e os objetos utilizados nas linguagens de programação; e o uso do relacionamento de um-para-muitos e muitos-para-muitos como forma de atender a normalização da base de dados que vem da ideia de reaproveitar os dados comuns ao invés de ter a mesma informação sendo repetida N vezes na mesma tabela, mas lembrando que o reaproveitamento vem com o uso de JOINs porque dependendo da forma normal aplicada vai separar os dados em diversas tabelas que posteriormente precisarão ser recomposta.

Outro ponto interessante é que os diversos bancos de dados vem tentando suprir algumas de suas fraquezas como: o banco de dados relacional que sempre está otimizando as consultas automaticamente, decidindo a ordem de executar os relacionamentos e escolhendo o melhor índice para melhorar o desempenho e o banco de dados orientados a documentos que permitem documentos aninhados (um-para-muitos).

O autor cita algumas diferenças entre o banco de dados relacional e não relacional, exemplo:

- Um banco de dados orientado a documentos tem a vantagem de ser **schemaless**, no qual não é necessário definir as colunas na criação do documento, e cada registro pode ter documentos com colunas diferentes, isso também é chamado de **schema-on-read**. Em um banco de dados relacional no qual criamos as tabelas definindo as colunas e seus tipos de dados dizemos que ele é **schema-on-write**;
- Um documento que já possui todos dados que precisa fica mais rápido de consultar, porque não há necessidade de fazer JOINs, mas é mais lento para atualizar porque precisa reescrever o documento inteiro;
- Bancos de dados relacionais estão adicionando suporte para XML e JSON, enquanto que banco de dados não relacional tem adicionado suporte a JOIN entre documentos.


### Linguagens de consulta

#### SQL

Junto com o modelo relacional, também veio a linguagem de consulta SQL que é declarativa assim sendo fácil e concisa; oculta implementações complexas e isso permite melhorar o desempenho (pensa assim, você está usando uma função como o APPROX_COUNT_DISTINCT na consulta SQL para contar a quantidade aproximada de valores distintos, como usuário do banco de dados não importa muito que é implementado usando o algoritmo HyperLogLog, mas amanhã se encontrarem uma forma mais otimizada ou com o mesmo desempenho mas com uma margem de erro menor, qualquer banco de dados pode mudar a implementação da função APPROX_COUNT_DISTINCT que todas as aplicações serão beneficiadas) e também permite a execução em paralelo (novamente imagine que a função internamente pode fazer uso do processamento paralelo utilizando diversos cores para executar mais rápido a operação desejada).

Aqui o autor também compara um código imperativo com a álgebra relacional e mostra como o SQL é mais similar a álgebra relacional porque você simplesmente identifica os padrões que você quer do dado.

Imagine que você quer contar quantos valores distintos têm em uma coluna, se for usar uma linguagem imperativa teria que pelo menos percorrer todos os valores usando um laço (while, do/while, for), guardar os valores distintos que foram encontrados e depois contar quantos valores encontrou; usando o linguagem declarativa do SQL você tem essa mesma função encapsulada dentro de um SELECT DISTINCT.


#### MapReduce

Aqui o autor também comenta sobre o MapReduce que é um modelo de processamento para grandes volumes de dados que foi popularizado pelo Google em 2004 e é suportado por diversos banco de dados e linguagens de programação.

O MapReduce não é declarativo e nem imperativo, a lógica da consulta é separada em duas funções, sendo uma para mapear os valores e outra para reduzir (agregar) os valores.

O autor também mostra um exemplo do MapReduce para consultar dados no MongoDB, mas o problema é a usabilidade porque tem que escrever as duas funções de forma coordenada junto com a consulta, também é importante saber que atualmente essa função foi descontinuada e atualmente o MongoDB sugere usar agregações no lugar do MapReduce.


### Modelo de dados em grafos

O **grafo** é composto de **vértices** (que representa as entidades) e **arestas** (que representa o relacionamento entre as entidades). O poder do grafo é o uso de diferentes tipos de objetos (vértices e arestas) no mesmo datastore e também por ser schemaless.

O autor cita que conforme aumenta o relacionamento de muitos-para-muitos entre as entidades é natural começar a modelar os dados usando grafos, como no caso de grafos sociais no qual as pessoas são as vértices e as arestas representam o quem conhece quem; ou no grafo da páginas web na qual a página é uma vértice e as arestas são os links que tem no conteúdo desta página apontando para outras páginas web.

O **Cypher** é uma linguagem de consulta declarativa para consultar em grafos e foi criada para o Neo4J, e é interessante que lendo uma consulta escrita em Cypher podemos encontrar diversas formas de como ela será executada e por ser declarativa é o banco de dados vai escolher a forma que for mais otimizada de execução.

No livro o autor também compara uma consulta SQL que precisa ser feita de forma recursiva para conseguir obter o resultado esperado, com uma consulta usando o Cypher que facilita percorrer os vértices do grafo para executar de forma bem mais simples a mesma consulta recursiva escrita no SQL.



No capítulo 3 o foco do livro será em como armazenar e recuperar os dados.