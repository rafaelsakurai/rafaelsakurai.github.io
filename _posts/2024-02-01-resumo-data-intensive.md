---
layout: post
title: "Resumo do livro Designing Data-Intensive Applications [Em construção]"
date: 2024-02-01 23:30:00
categories: [ Livro, Designing Data-Intensive Applications, Dados Distribuídos, Processamento Batch, Processamento Stream ]
tags: [livro, dados distribuídos, replicação de dados, particionamento de dados, processamento batch, processamento stream]
image: assets/images/posts/2024-02-01-resumo-data-intensive.png
excerpt: Resumo comentado dos capítulos do livro Designing Data-Intensive Applications do Martin Keplemmann.
featured: true
hidden: true
---


O livro [Designing Data-Intensive Applications](https://dataintensive.net) do autor [Martin Keplemmann](https://twitter.com/martinkl) é dividido em três partes que aborda a **fundação dos sistemas de dados** (disponibilidade, escalabilidade e manutenibilidade de aplicações, modelos de dados, linguagens de consulta, armazenamento e recuperação de dados, codificação e evolução), **dados distribuídos** (replicação, particionamento, transações, problemas com sistemas distribuídos, consistência e consenso) e **derivados de dados** (processamento em batch, processamento em stream e o futuro dos sistemas de dados)

Se você trabalha com dados, o que envolve todos os diversos tipos de aplicações, e a aplicação já passou da fase em que os inserts e queries no banco de dados estavam rápidos, um único banco de dados transacional possuía todas as funcionalidades necessárias para a aplicação, os relatórios não precisavam esperar para serem gerados, o envio ou recebimento dos dados era pouco e facilmente transferido no formato JSON para algum serviço REST, o arquivo de log era pequeno e cabia vários dias ou meses dentre de uma única máquina, mas bem, isso são águas passadas e agora você está entrando em mar aberto e tem um oceano de dados com diversos desafios novos para conseguir transferir (receber ou enviar), armazenar, manipular, recuperar e processar tudo isso, acho que o livro poderá te ajudar ou pelo menos direcionar seus esforços para resolver os novos problemas.

Conforme eu for lendo os capítulos, pretendo fazer resumos com anotações e comentários meus, então talvez algum exemplo ou explicação que coloquei você não encontre no livro, porque foi algo que adicionei para facilitar o meu entendimento, mas se o resumo do livro te interessar recomendo que você leia na íntegra porque terá muito mais detalhes e explicações.


### Parte 1 - Fundação dos sistemas de dados

Imagine o começo de uma aplicação web que tem diversos CRUDs (cliente, endereço, produto, fornecedor, funcionário, pedido, etc), tem relatórios (vendas, clientes, atendimentos, etc) e fluxo de navegação do site; ou uma aplicação que controla os caminhos de uma transportadora e quer monitorar tanto as entregas realizadas como também rastrear em tempo real onde está cada caminhão para poder analisar as rotas ou se ocorre algum problema; ou um jogo online que registra todas as ações do jogador como construções realizadas, pontuações, ataques contras outros jogadores, moedas virtuais compradas para usar no jogo e mantém tudo registrado para caso ele perca a conexão com a Internet você consiga recuperar o jogo de onde parou ou o mais próximo possível; ou qualquer outro cenário de aplicação que envolve o armazenamento e consulta dos dados.

Além do tipo da aplicação também tem que cuidar das camadas back, front, mobile segurança, controlar transações, cuidar da disponibilidade da aplicação, ambiente de homologação e produção, cuidar do banco de dados, integração com sistemas de terceiros, versionamento do código, usar o ambiente de nuvem, etc, aqui já temos diversos problemas que precisamos estar constantemente atentos conforme a aplicação é desenvolvida e usada em produção, até aqui você já tem todo o conteúdo de uma graduação de faculdade e talvez de uma pós-graduação também.

A primeira parte do livro aborda alguns desafios que fazem parte da base das aplicações que usam **dados** de forma mais intensa, pode ser porque essa aplicação transacional cresceu e agora o volume de clientes, requisições ou transações está muito maior que no começo; ou pode ser que o sistema já tenha sido projetado para receber, processar, armazenar e disponibilizar grandes volumes de dados.

**Uma aplicação que faz uso intensivo de dados começa a ter outro conjunto de problemas que precisa solucionar no qual os dados em si são o principal desafio**, porque agora há:
- **grande volume de dados**, que podem ser tanto em questão de acessos simultâneos, registros armazenados no banco de dados, espaço para armazenar os dados, etc, então envolve diversos pontos como suportar mais requisições, melhores estratégias para armazenar os dados, melhores estratégias para consultar os dados, etc, exemplo: como fazer aquele mesmo relatório de vendas que antes consultava algumas centenas de registro e passa a consultar milhares de registros;
- **dados complexos**, sendo necessário realizar a integração entre dados de diversas origens e formatos, imagine agora você fazendo um join entre um CSV, um JSON e uma tabela do banco de dados, ou fazendo join entre um arquivo de logs e os registros dos clientes da empresa para gerar um dashboard, a partir do momento que você tem uma camada que abstrai o formato dos dados, fica mais fácil manipular diversos formatos de dados ao mesmo tempo;
- **velocidade com que os dados mudam**, as aplicações continuam crescendo e evoluindo com o passar do tempo, surgem novas funcionalidades, o esquema das tabelas muda, novas fontes de dados podem ser incorporadas, novos formatos de dados podem ser suportados.


[Capítulo 1 - Disponibilidade, escalabilidade e manutenibilidade de aplicações](https://www.sakurai.dev.br/resumo-data-intensive-cap-01)

[Capítulo 2 - Modelos de dados e linguagens de consulta](https://www.sakurai.dev.br/resumo-data-intensive-cap-02)

[Capítulo 3 - Armazenar e recuperar os dados](https://www.sakurai.dev.br/resumo-data-intensive-cap-03)

Capítulo 4 - Encoding e Evolução


### Parte 2 - Dados distribuídos

Na primeira parte do livro o foco eram os sistemas em que os dados estavam armazenados em uma única máquina, agora ficou mais intenso o uso dos dados e é necessário um ambiente distribuído para conseguir armazenar, processar ou consultar esses dados.

Capítulo 5 - Replicação

Capítulo 6 - Particionamento

Capítulo 7 - Transações

Capítulo 8 - O problema com os sistemas distribuídos

Capítulo 9 - Consistência e consenso


### Parte 3 - Derivados dos dados

O foco desta parte do livro é a integração entre diferentes sistemas de dados, como transferir grandes volumes de dados entre sistemas diferentes com arquiteturas e armazenamento de dados diferentes.

Capítulo 10 - Processamento em Batch

Capítulo 11 - Processamento em Stream

Capítulo 12 - O futuro dos sistemas de dados
