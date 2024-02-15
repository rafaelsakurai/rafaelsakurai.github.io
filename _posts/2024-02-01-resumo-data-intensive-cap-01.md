---
layout: post
title: "Resumo do capítulo 1 do livro Designing Data-Intensive Applications"
date: 2024-02-01 23:45:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, desenvolvimento de aplicações, disponibilidade, escalabilidade, manutenibilidade]
excerpt: Este primeiro capítulo o autor aborda três grandes pilares relacionados ao desenvolvimento de aplicações (disponibilidade, escalabilidade e manutenibilidade).
featured: false
hidden: true
---

## Capítulo 1 - Disponibilidade, escalabilidade e manutenibilidade de aplicações

Uma aplicação que faz uso intensivo de dados começa a se preocupar com diversos pontos que envolvem os dados, como: armazenamento (banco de dados relacional, banco de dados não relacional, arquivos, etc), cache (leitura rápida de dados pré processados), buscas indexadas (o usuário pode pesquisar os dados usando diversos filtros), processamento via stream (no qual os dados são recebidos, processados e agregados continuamente aos dados já existentes) e processamento via batch (quando os dados podem ser acumulados por um período de tempo (horas ou dias) antes de ser processado).

Neste primeiro capítulo o autor aborda três grandes pilares relacionados ao desenvolvimento de aplicações: disponibilidade, escalabilidade e manutenibilidade.

### Disponibilidade - falhas de hardware, software e humanas

O autor começa abordando se a aplicação funciona como deveria, se tolera erros humanos ou uso de modo inesperado, se tem um bom desempenho com base na carga e volume de dados esperado, se previne acessos não autorizados ou evita abusos de acessos.

Quando começamos o desenvolvimento de uma aplicação web, temos em mente uma quantidade média de acessos, registros novos serão adicionados em cada tabela do banco de dados, ou registros que serão consultados para gerar um relatório. Existem diversas formas de tentar simular essa carga e medir o tempo de resposta, normalmente chamamos isso de Teste de Stress e queremos simular como será o comportamento real da aplicação.

Durante o uso da aplicação podem ocorrer exceções em simples componentes porque a ação efetuada desvia da forma e comportamento esperado, e também podem ocorrer falhas que causam a indisponibilidade do serviço ou da aplicação como um todo.

Quando pensamos em exceções ou falhas tenha em mente durante o desenvolvimento que em algum momento o hardware irá falhar, como: uma máquina que fica indisponível, o armazenamento em disco que acabou, estourou o uso da memória ou perda de conexão com a Internet, e se você desenvolver o sistema levando em consideração que esses problemas irão ocorrer mais cedo ou mais tarde é certeza de que você terá uma aplicação melhor, porque desenvolverá a aplicação pensando em como ela deverá se recuperar em caso de falha e como fornecer uma alternativa para o usuário ao invés de uma mensagem com o stack trace do erro.

Durante o desenvolvimento é necessário tomar cuidado com bugs, que ocorrem até nas melhores aplicações, mas podemos minimizar aplicando um bom conjunto de testes (unitários, integração, sistema, etc); também podemos ter problema com diversas aplicações executando na mesma máquina que começam a competir pelos recursos e precisa tomar cuidado para nenhuma das aplicações consumir demais os recursos compartilhados atrapalhando as outras aplicações; cuidado com as falhas em cascata quando um componente que chama outro componente ou aplicação que falha e lança um erro, acaba fazendo diversos componentes pararem de funcionar.

Uma aplicação que não consegue executar porque acabou o espaço em disco, o insert que não funciona mais porque atingiu o limite do tamanho de um campo ou o banco de dados que parou de funcionar porque não tem mais espaço em disco para armazenar os registros, acho que você pode pensar em diversos outros problemas que também podem ocorrer com a aplicação simplesmente pelo fato de não haver um mínimo de monitoramento que possa avisar que você está correndo de olhos fechados e logo vai dar com a cara no muro que está na sua frente.

O autor também cita uma pesquisa interessante de 2003 sobre o porque os sistemas disponíveis na Internet falham e a pesquisa mostra que a maior causa de erros ocorrem devido a alguma configuração que alguma pessoa fez errado. Apesar desta pesquisa ser de 2003, se você olhar a lista atual das [Top 10 Vulnerabilidades](https://owasp.org/www-project-top-ten) mais comum das aplicações web vai ver que sempre tem o problema da "configuração de segurança errada" ou se você procurar sobre o assunto na Internet vai encontrar algo como essa notícia falando que  [o erro humano é a principal causa dos ransomwares (sequestro dos dados) nas plataformas de núvem](https://www.thalesgroup.com/en/worldwide/security/press_release/2023-thales-data-threat-report-reveals-increase-ransomware-attacks).

E para fechar essa parte de disponibilidade o autor pergunta o porquê a disponibilidade da aplicação é importante? Pense aí no contexto da aplicação que você trabalha, o que ocorre caso ela permaneça indisponível por alguns minutos, horas ou dias. Na prática, a falta de disponibilidade causa perda de produtividade, vendas e a reputação da empresa.


### Escalabilidade - carga e desempenho

Conforme aumenta o uso da aplicação tanto na quantidade de usuários quanto de serviços oferecidos é comum que ocorra um aumento de carga, se você não desenvolveu o sistema pensando em como ele se comportaria com 10 ou 100 vezes mais carga de trabalho, pode ser que você tenha surpresas, as telas começam a demorar para carregar, uma consulta começa a demorar mais que segundos para executar, o relatório com dados do ano inteiro já não executa no tempo esperado e você recebe um erro de timeout, o número de requisições é tão grande que o servidor de aplicações web não consegue atender toda a demanda, etc.

O autor cita como exemplo o Twitter (agora X) que em 2012 recebia em média 4.6 mil postagens (escrita) e 300 mil requisições da timeline (leitura) por segundo. Dependendo da pessoa ela tinha alguns seguidores ou alguns milhares de seguidores, então o Twitter precisou mudar a estrutura para poder ter um cache que permitisse a leitura rápida dos tweets apresentados nas timelines.

O site DOMO costuma montar um infográfico anualmente chamado [Data Never Sleeps](https://www.domo.com/data-never-sleeps) que mostra o volume de dados que algumas plataformas recebem por minuto, e usando como exemplo o Twitter, no ano de 2022 a quantidade de postagens foi de 347 mil por minuto (ou uma média de quase 6 mil postagens por segundo).

Dependendo da aplicação, você vai ter métricas diferentes para medir o desempenho, exemplo: se é uma aplicação que realiza um processamento em batch via Hadoop ou Spark é importante medir a quantidade de requisições processadas por segundo, mas se é uma aplicação web acessada de forma online você vai querer medir o tempo de resposta das requisições. 

Um ponto importante é que mesmo que a aplicação faça diversas tarefas em paralelo para minimizar o tempo de processamento, se o processamento de uma das tarefas for lento, então toda a requisição é afetada e aumenta o tempo de resposta.

Se sua aplicação foi desenvolvida pensando nesse aumento da carga de processamento, tanto para um crescimento contínuo como para suportar cargas que ocorrem periodicamente (como no caso de algumas aplicações que recebem uma demanda muito maior em alguns períodos do ano), você tem um **sistema que é elástico**, pois suporta **scaling up** (crescimento vertical usando máquinas mais potentes) ou **scaling out** (crescimento horizontal com mais máquinas realizando o processamento de forma distribuída).

E aqui tem um ponto que reflito quando ocorre um problema de desempenho é se o que está implementado não pode ser feito de outra forma mais otimizada (antigamente chamávamos de escovar bits), será que o código não está fazendo mais do que deveria, será que as consultas não poderiam ser mais otimizadas, será que o dado não poderia ser armazenado de outra maneira para facilitar seu uso, será que precisa trafegar todos esses dados na requisição e na resposta, etc, existem vários pontos para olhar e tentar melhorar antes de simplesmente continuar com o mesmo sistema só aumentando o número de máquinas. Mas convenhamos, inicialmente pode até ser mais fácil e barato convencer alguém de que precisa colocar uma máquina mais potente ou mais máquinas para realizar que a aplicação funcione "mais rápida", do que explicar que alguém vai fazer uma tarefa de refatoração e que no final da tarefa as funcionalidades vão continuar as mesmas, mas fazendo o mesmo trabalho de forma mais otimizado. Aqui é um ponto de escolha, às vezes na pressa escolhemos mais máquinas e pagamos o preço, as vezes conseguimos segurar com o desempenho atual enquanto trabalhamos em uma refatoração para melhorar no futuro, as vezes conseguimos os dois tanto aumentar a quantidade de máquinas conforme a demanda da aplicação, mas também aplicar uma refatoração para que o consumo de recursos ocorra de maneira mais otimizada possível economizando o dinheiro da empresa.

E para fechar essa parte do desempenho o autor comenta que há diversos fatores que influenciam o desempenho da aplicação e não há uma única arquitetura capaz de resolver qualquer tipo de problema, o importante é desenvolver a aplicação de modo que possa ser possível mudar rapidamente para suportar mais carga de trabalho.


### Manutenibilidade - Operacionalidade, simplicidade e capacidade de evolução

O maior custo não é desenvolver um sistema do zero, o maior custo é dar manutenção uma vez que o sistema já está funcionando em produção, novas funcionalidades são continuamente adicionadas e é necessário fornecer o suporte técnico do que já está em uso.

Muitas pessoas encaram o desenvolvimento em uma aplicação legada como algo ruim, porque: se está feito desse jeito e está funcionando não tem porque mudar; ou faz como o resto do sistema está fazendo; tantas pessoas já mexeram nesse código que fica difícil compreender o que realmente deveria fazer; etc, mas todo legado deveria ter a opção de evoluir, de ser melhorado e simplificado.

Sobre esse assunto, o autor foca em três princípios de desenvolvimento de software: **operabilidade**, os processos são automatizados, possui boa documentação, é fácil reconhecer os problemas, etc; **simplicidade**, boas abstrações ajudam a esconder códigos complexos, e um item interessante abordado pelo autor é como o SQL abstrai a complexidade que envolve as operações e linguagens de programação que facilitam o desenvolvimento escondendo a complexidade; **capacidade de evolução**, o sistema muda constantemente e deve ser criado pensando em facilitar as futuras mudanças.

Quem gosta de ficar arrumando bug em produção de madrugada ou corrigindo códigos que poderiam ter sido implementados de forma correta logo na primeira vez, na prática muitas equipes de desenvolvimento adotam padrões para constantemente melhorarem o código, então para ser um desenvolvedor melhor é necessário estar sempre aprendendo coisas novas, o código que você escreveu ontem já é legado, aproveite hoje para aprender a fazer só um pouco melhor aquilo que você fez ontem, tenho certeza que não é só seu código que vai melhorar, mas sua carreira também vai melhorar.

O autor apresenta várias sugestões para atender esses três princípios, mas em resumo é pensar em como facilitar a vida de quem usa e desenvolve o sistema.

No [capítulo 2](https://www.sakurai.dev.br/resumo-data-intensive-cap-02) o livro aborda o tema dos modelos de dados e linguagens de consulta, comentando sobre como o dado é transformado entre as camadas da aplicação, os diversos tipos de banco de dados e linguagens para consultar os dados.