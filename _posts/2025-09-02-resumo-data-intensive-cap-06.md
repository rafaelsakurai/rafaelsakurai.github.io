---
layout: post
title: "Resumo do capítulo 6 do livro Designing Data-Intensive Applications"
date: 2025-09-02 21:41:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, particionamento, otimização de leitura e escrita]
excerpt: Neste capítulo o autor aborda o conceito de particionamento na qual temos grandes volumes de dados que serão divididos de tal forma que cada registro pertence a apenas uma partição, essa função fornece um ganho de performance tanto na escrita como na leitura dos dados.
featured: false
hidden: true
---

No [capítulo 5](https://www.sakurai.dev.br/resumo-data-intensive-cap-05) o autor aborda o conceito de replicação de dados como uma forma de manter o dado distribuído em diversos servidores ou datacenters, servindo como redundância dos dados em caso de alguma falha, maior disponibilidade do dado, etc.


## Capítulo 6 - Particionamento

Neste capítulo é abordado o conceito de particionamento na qual temos grandes volumes de dados que serão divididos de tal forma que cada registro pertence a apenas uma partição, essa função fornece um ganho de performance tanto na escrita como na leitura dos dados.

Gosto bastante desse assunto porque o impacto causado na otimização das escritas e consultas pode ser perceptível tanto no tempo despendido, economia de recursos e consecutivamente economia de custo.

Dado a situação em que você tem um grande volume de dados, mesmo criando índices e otimizando consultas, ainda há problemas relacionados de acordo com as necessidades de quais dados precisam ser consultados e provavelmente você já passou pela situação de com o passar do tempo perceber que as consultas demoram cada vez mais para retornar os dados, dado que esse grande volume de dados na prática só tende a continuar crescer, precisamos encontrar alguma estratégia para voltar ou melhorar o desempenho das consultas.

Os dados podem possuir alguma informação que provavelmente também é usada na consulta e que serve como base para separar os dados em grupos de quantidades similares, pode ser uma data, uma categoria, uma opção entre os tipos possíveis para uma determinada coluna, uma combinação de mais de uma coluna, etc.

Há um cuidado na escolha da partição para evitar que ocorram partições que fiquem desproporcionalmente maiores do que outras ou partições tão pequenas que as buscas pelas partições que precisam ser consultadas acabam atrapalhando o desempenho da consulta.

O autor apresenta algumas estratégias para definir o particionamento dos dados:
- A chave é o valor de uma coluna;
- A chave é um intervalo de valores;
- A chave é um Hash que distribui de forma uniforme os dados;

A escolha das partições influenciará diretamente na performance das consultas, porque pode ser que o banco de dados consiga percorrer apenas o mínimo de partições para retornar o resultado da consulta, como também pode fazer com o que o banco tenha que percorrer todas as partições para encontrar o que está sendo consultado.

Com o passar do tempo as coisas vão mudando no banco de dados:
- Aumenta a quantidade de registros, seu armazenamento e memória RAM para processar as requisições;
- Aumenta o tempo de resposta das consultas;
- A máquina pode ter problemas e ser necessário trocar.

Já passei por sistemas em que foi necessário aumentar o tamanho das colunas de índice porque estavam alcançando o limite do tamanho configurado na criação. Já vi máquinas que precisaram ganhar mais disco e ram para o banco continuar funcionando e melhorar sua performance. Já vi bancos que foram migrados para um hardware completamente novo, passando de uma taxa de 95% do uso contínuo da CPU para apenas 5% de uso da cpu no novo hardware. Infelizmente já vi até downgrade de banco de dados que tinha uma máquina dedicada e foi migrada para uma máquina virtual para economizar recursos e deixou todo o sistema muito mais lento.

E quando trabalhamos com partições pode ser necessário chegar ao ponto de fazer o rebalanceamento das partições, mudando o dado de um nó do cluster para outro nó isso serve para distribuir a carga de uma partição.

Além de separar o dado em partes permitindo ler apenas algumas partes do dado ao invés do todo, outra vantagem está relacionado no processamento paralelo dos dados, os bancos de dados podem dividir e executar em paralelo o processamento entre as partições para otimizar as consultas.

O próximo capítulo o autor aborda o conceito de transação, explicando o conceito de ACID (Atomicidade, Consistência, Isolamento e Durabilidade) implementado por diversos bancos de dados, níveis de isolamento dos dados quando múltiplas transações querem alterar os mesmos registros e quando várias transações são executadas concorrentemente (como uma única transação que precisa escrever em mais de um banco de dados).