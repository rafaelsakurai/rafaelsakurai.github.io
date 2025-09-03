---
layout: post
title: "Resumo do capítulo 5 do livro Designing Data-Intensive Applications"
date: 2025-09-02 21:20:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, replicação, disponibilidade dos dados]
excerpt: Neste capítulo o autor aborda o conceito de replicação dos dados que está relacionado com a forma como disponibilizamos o mesmo dado em mais de uma máquina a fim de garantir o acesso rápido, recuperação em caso de desastre, etc.
featured: false
hidden: true
---

No [capítulo 4](https://www.sakurai.dev.br/resumo-data-intensive-cap-04) o autor aborda o formato no qual os dados são armazenados em disco, como isso influência na leitura e escrita, como o formato influência no tamanho do arquivo em disco e a importancia do uso do esquema do dado para arquivos estruturados e não estruturados.


## Capítulo 5 - Replicação

(Após você ler esse começo sobre replicação, compare com seu ambiente de trabalho, veja se não ocorrem situações similares entre líder e funcionário, tanto quando entra um novo funcionário que precisa fazer on-board, como quando troca o líder e um novo precisa ser escolhido, etc)

A ideia da replicação é manter diversas cópias dos dados em diversas máquinas para que o acesso fique mais rápido geograficamente (imagine o filme da Netflix sendo replicado em diversas centrais, porque todos usuários querem começar a assistir assim que apertam o play, sem esperar muito tempo para fazer o download dos dados entre continentes separados); cópias de segurança para em caso de desastre conseguir recuperar os dados (às vezes com um limite mínimo de KMs entre os locais onde serão armazenados os dados); ou mais cópias para que as máquinas possam ler e disponibilizar os dados solicitados com mais rapidez (similar ao cluster de máquinas adicionados ao front de uma aplicação ou site com grande acesso e que precisa disponibilizar rápido algumas páginas para seus usuários, principalmente o que é apresentado na home).

Este capítulo mostra um assunto bem mais específico, você chegou em um ponto que precisa replicar seu dado, provavelmente porque o acesso (leitura ou escrita) do conteúdo começou a impactar na performance da sua aplicação. Algumas coisas conseguimos facilmente configurar em uma plataforma de cloud para resolver este problema de replicação, mas será que você pelo menos sabe o que está acontecendo por baixo dos panos e possíveis problemas que podem ocorrer?

Se o dado não muda com o tempo (como um log de requisições), então é mais simples replicar porque precisa apenas copiar os dados para as réplicas, toda a dificuldade da replicação acontece quando os dados mudam e o foco deste capítulo é quando isso acontece.

Cada máquina com uma cópia dos dados é chamada de réplica, quando temos múltiplas réplicas, vem o problema: como garantir que todas as réplicas estão atualizadas?

A ideia básica das replicações é o uso de líder(es) e seguidor(es), na qual toda escrita é enviada para uma réplica líder que irá repassar um log ou stream de mudanças para as demais réplicas (seguidores) atualizarem suas cópias, e a leitura pode ser feita no líder ou em qualquer réplica.

Agora vem outro problema, como garantir que o usuário está lendo a réplica que está atualizada com o dado mais recente? Se um usuário A alterar um registro e a escrita é na réplica líder, e o usuário B lê o mesmo registro de uma réplica seguidor que ainda não recebeu o log da mudança, cada réplica manterá por algum tempo valores diferentes para o mesmo registro.

A replicação das mudanças podem acontecer de modo síncrono ou assíncrono:
- Replicação síncrona: o líder espera atualizar a réplica antes de retornar para o usuário que a escrita foi feita com sucesso e permitir a leitura dos novos valores, a vantagem é ter a garantia que o dado está atualizado em todas as máquinas, a desvantagem é que aguarda a confirmação das réplicas e se uma réplica estiver fora não escreve no líder;
- Na replicação assíncrona: o líder envia o log da mudança para as réplicas e não aguarda uma resposta para retornar para o usuário, a vantagem é que o líder pode continuar fazendo novas escritas sem aguardar as réplicas, a desvantagem é que se o líder cair, pode ser que tenha escritas que não foram replicadas e se a consulta é feita na réplica pode obter algum dado temporariamente desatualizado.

Há também a opção da configuração semi-síncrona, isso indica que uma das réplicas será atualizada de forma síncrona enquanto que as demais réplicas serão atualizadas de forma assíncrona, e caso a réplica síncrona fique indisponível, então uma das réplicas assíncrona substituirá a réplica síncrona.

Adicionar novas réplicas (seguidores) também não é um processo simples, você não vai apenas copiar toda a base e carregar na réplica, porque até isso terminar de ocorrer seu dado já está desatualizado, e você também não vai fazer o bloqueio (lock) da base de dados porque as transações ocorrem constantemente. Para resolver isso, precisamos tomar um pouco mais de cuidado e podemos fazer assim:
- Pega o último snapshot da base de dados e carrega na nova réplica;
- A nova réplica solicita para o líder todas as alterações desde a geração do snapshot;
- Quando termina de atualizar os dados avisa o líder que está pronto e começa a receber as informações como qualquer outra réplica.

Em caso de falha do seguidor precisando reiniciar a réplica, ocorre algo parecido com a adição de uma réplica, a diferença é que já tem uma base carregada, após reiniciar só precisa solicitar ao líder as últimas alterações.

Caso o líder falhe, precisará:
1- Identificar que o líder está indisponível;
2- Selecionar uma réplica para substituir como novo líder;
3- Configurar o sistema para utilizar este novo líder e avisar as demais réplicas quem é o novo líder.

Nessa situação podem ocorrer alguns problemas, como:
- O líder voltar a funcionar e temporariamente as escritas serem enviadas para os dois líderes, nesse caso deve descartar as escritas no líder antigo;
- Uma réplica desatualizada pode ser promovida a líder, nesse caso o descarte das escritas no líder antigo podem causar perda de dados;
- Duas réplicas assumem como líder (split brain), nesse caso ambos aceitaram escritas e poderemos perder informações quando identificados e apenas um dos líderes for mantido;
- Quanto tempo devemos aguardar até o líder ser considerado inativo (timeout)?

A seguir o autor apresenta quatro formas de implementar a replicação:
1- Replicação com base nos procedimentos - replica as operações de insert, update e delete nas réplicas seguidoras, mas pode ter problema de consistência, ex: se usar funções não determinísticas como now() ou rand() vai gerar valores diferentes em cada réplica; colunas com incremento automático precisam ser executados exatamente na mesma ordem. 

2- Envio com log de escrita (WAL - write-ahead log) - as escritas no líder são armazenadas em logs e depois o log é executado nas réplicas, a principal desvantagem é que os logs normalmente possuem informações de baixo nível chegando até detalhes sobre mudança nos blocos do disco, a vantagem é que alguns bancos de dados permitem que as réplicas possuam uma versão mais recente do software assim todas as réplicas são atualizadas primeiro e depois o líder é desligado para ser substituído por uma das réplicas, assim conseguindo atualizar a versão do banco de dados sem precisar parar.

3- Replicação lógica do log (com base nas linhas) - um log lógico com a sequência de escritas a nível de linhas das tabelas, no caso de uma transação as linhas serão seguidas por um registro que indica o commit. Essa opção traz a vantagem de poder usar versões diferentes do banco de dados entre o líder e as réplicas, e também traz a opção de usar bancos de dados diferentes, porque o log pode ser facilmente convertido para outros bancos de dados.

4- Replicação com base em trigger - alguns bancos de dados suportam triggers e stored procedures que podem ser executadas a cada alteração para gerar um log dessas alterações e esse log pode ser usado por algum processo externo para carregar as mudanças para outra base de dados. Tem a vantagem de ser bem flexível, mas a desvantagem de adicionar uma sobrecarga a mais de trabalho.

A replicação tem como objetivo aumentar a escalabilidade (processando mais requisições do que uma única máquina) e reduzir a latência (com réplicas próximas do usuário).

Dado a situação na qual a aplicação recebe mais requisições de leitura do que de escrita, podemos adicionar mais réplicas para aumentar a capacidade de atendimento, isso é chamado de read-scaling architecture. Mas essa arquitetura só é eficiente quando a replicação é assíncrona, porque você quer uma resposta rápida e não quer esperar que cada transação de escrita atualize todas as réplicas antes de ficar disponível para novas requisições de leitura.

Partindo do ponto que a replicação é assíncrona, pode ocorrer inconsistências na réplica, porque pode estar com o dado desatualizado, então uma mesma consulta pode retornar resultados diferentes se feita em réplicas diferentes, porque há um tempo necessário para refletir a escrita em todas as réplicas.

Esse lag na replicação pode ser de milliseconds, mas não há um limite de tempo para finalizar toda a replicação, então o tempo de atualização das réplicas pode dar a sensação que a operação não foi realizada, caso seja consultado o resultado de uma operação antes do término da replicação.

Uma forma de resolver esse problema é usando uma consistência de leitura após a escrita que pode ser feita:
- Lendo os dados do usuário a partir do líder e os demais dados a partir das réplicas;
- Após a data da última atualização, no próximo minuto irá ler os dados do líder e depois das réplicas, para dar tempo de sincronizar todas réplicas.

Alguns cuidados com essa abordagem são: quando o dado é distribuído em data centers geograficamente distribuídos precisa controlar as rotas e quando o usuário pode acessar o dado a partir de diversos dispositivos precisa manter a consistência entre os dispositivos. 

### Replicação com múltiplos líderes

Quando temos um ambiente de replicação com apenas um líder, podemos ter o problema de não conseguir acessar esse líder (a máquina pode ter desligado, o serviço travou, falha na rede, etc) e nesse caso vai parar todas escritas no banco de dados.

A extensão natural quando temos esse problema é utilizar replicação com múltiplos líderes, então podemos ter vários nós aceitando requisições de escrita.

O autor já começa esse assunto dizendo que raramente faz sentido ter múltiplos líderes porque o benefício raramente compensa a complexidade adicional, mas o autor também cita alguns exemplos que esse aumento de complexidade pode ser benéfico:
- Quando temos réplicas em datacenters geograficamente distribuídos e para evitar falhas e aumentar performance,  disponibilizamos um líder para cada data center. O problema nessa situação é tratar conflitos de escrita;
- Quando o cliente utiliza uma aplicação que precisa continuar funcionando mesmo offline e depois em algum momento sincronizar. Nesta situação o dispositivo tem uma base local que serve como líder enquanto estiver offline e assim que possível realiza um processo de sincronização assíncrono com outro líder para replicar o dado enquanto estava offline;
- Diversas pessoas editando o mesmo documento simultaneamente. A edição é aplicada localmente e depois sincronizada com as outras pessoas que estão usando o mesmo aplicativo e para evitar o bloqueio o ideia é controlar pequenas mudanças.

O maior problema quando usamos replicação com múltiplos líderes é tratar os conflitos na escrita. O conflito ocorre quando duas ou mais escritas estão concorrendo alterando o mesmo registro. O autor apresenta algumas formas de evitar e tratar o conflito.

Lembrei que uma vez precisei tratar conflitos porque tinha um formulário que precisava ser preenchido semestralmente e tinha um prazo para fazer isso (era um formulário com informações referentes ao plano de ensino da disciplina na faculdade) e várias pessoas tinham permissão para preencher esse formulário (qualquer professor da disciplina no semestre letivo poderia preencher).

Para evitar um possível conflito, usei o framework JPA do Java que identifica conflitos usando um atributo de versão nos registros, quando você começa a preencher o formulário novo, seu registro vai ser salvo com a versão 1, depois a cada alteração essa versão é incrementada. Se duas pessoas começarem a preencher o formulário do zero ou editar ao mesmo tempo a partir de uma versão, o primeiro que salvar vai ter a versão incrementada, o segundo a salvar vai ganhar uma exceção e o tratamento dessa exceção era mostrar para o usuário que o formulário foi atualizado por outra pessoa e mostrar qual o conteúdo atual do formulário, assim o usuário atual pode verificar se o que está preenchido já está pronto ou se ele vai querer modificar algo e atualizar a versão atual. Essa foi uma abordagem similar ao tratamento de conflito quando fazemos um merge no git.

Na replicação com múltiplos líderes há uma definição da topologia de como as escritas serão replicadas entre os líderes, basicamente temos as topologias: circular, estrela e todos-para-todos. A topologia todos-para-todos é a mais comum, cada líder quando recebe uma escrita, envia essa escrita para todos os demais líderes.

### Replicação sem líder

Como não tem um líder, o cliente envia a escrita para diversas réplicas e não haverá quem determinará a ordem das escritas, essa diferença no design impacta na forma como usamos a base de dados.

Quando não há um líder, pode ocorrer de enviar uma escrita para todas as réplicas, mas alguma réplica estará indisponível no momento. Quando a réplica que estava indisponível voltar, ela estará desatualizada, nesse momento uma leitura consultando o dado escrito pode retornar uma informação desatualizada.

Quando a réplica volta a funcionar há um processo de reparação da informação para essa réplica ficar atualizada com as demais réplicas, mas isso demanda um tempo.

Para resolver está situação, podemos usar um quorum para leitura e escrita, a ideia é definir uma quantidade mínima de votos (retorno com sucesso) requeridos para validar a leitura e escrita. Então se você vai gravar ou ler uma informação, essa operação será enviada para várias réplicas e a contagem dos votos retornados indicaram se a operação foi executada.

Já usei replicação de banco de dados e é interessante como o próprio banco de dados resolve isso abstraindo para a camada da aplicação.

O [capítulo 6](https://www.sakurai.dev.br/resumo-data-intensive-cap-06) aborda o particionamento dos dados como uma forma para melhorar a organização do dado, conseguir distribuir o dado em várias máquinas e também otimizar o uso desse dado.