---
layout: post
title: "Resumo do capítulo 4 do livro Designing Data-Intensive Applications"
date: 2024-08-07 07:20:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, encoding dos dados, evolução do schema, formato do arquivo]
excerpt: Neste capítulo o autor aborda o conceito de códificação e como o formato que salvamos os dados em disco influência para ler, escrever e também o espaço de armazenamento em disco, e também aborda a importância do esquema dado que podemos ter arquivos com dados que serão estruturados e não estruturados.
featured: false
hidden: true
---

No [capítulo 3](https://www.sakurai.dev.br/resumo-data-intensive-cap-03) o autor aborda como os dados são armazenados e recuperados, como construir um index para melhorar a recuperação das informação, diferença entre OLAP e OLTP, armazenamento orientado a colunas e materialized view.


## Capítulo 4 - Encoding e Evolução

Quando trabalhamos com dados, é comum encontrar dados estruturados ou semiestruturados que utilizam um schema para identificar sua estrutura, os bancos de dados fazem isso, também temos arquivos que seguem um formato para facilitar o uso dos seus dados como um CSV que separa os dados por vírgula ou um JSON que possui tags de abertura e fechamento para delimitar o valor se cada atributo, ou arquivos que possuem um schema aplicado durante sua escrita e leitura.

O schema pode ser aplicado na leitura e na escrita dos dados para garantir que todos os dados armazenados estão no mesmo formato; mas também pode ter um formato flexível que não usa um schema na escrita permitindo diferentes versões dos dados, isso é chamado de schemaless, você aplica o schema apenas na leitura, assim você conseguirá ter diversas versões do dado que foi mudando ao longo do tempo.

Com que frequência seus dados mudam? Exemplo: mudar o tipo do dado para algo mais restrito (como mudar de um double para inteiro) é algo difícil de ocorrer depois que o schema já está definido, o que acontece é mudar para um tipo menos restrito (como mudar de um inteiro para string, inteiro longo ou double); mas se o dados foi definido com um tamanho e já está chegando no seu limite é possível aumentar o tamanho, como uma coluna inteira definida com tamanho de 6 dígitos e já chegou perto do limite e precisa ser alterado para suportar um número com mais de 6 dígitos.

A evolução dos dados está relacionada a quão fácil é feita às mudanças ao longo do tempo, por isso é importante conhecer o tipo do dado e quais valores pretendemos guardar no campo deste tipo, e também como adicionar ou remover um campo nessa mesma estrutura.

Definir um encoding (formato) para armazenar os dados está relacionado a entender como os dados serão armazenados, internamente se você usa um banco de dados (relacional ou não relacional), entenda como os dados serão armazenados porque essa pode ser sua base para depois entender como você pode melhorar o desempenho de uma consulta, se os dados são armazenados em disco, entenda qual o tipo de sistema de arquivos, qual o formato do arquivo (CSV, XML, JSON, Avro, Parquet, etc) porque com isso você saberá no mínimo que um JSON ocupa muito mais espaço em disco do que um Parquet que é compactado por padrão; que se você quer algumas colunas em específico a consulta ficará mais rápida no Parquet (que já armazena separados por coluna) do que no CSV (que armazena todos os valores de um registro na mesma linha); que se você precisar mesclar o conteúdo (todos os valores) ficará mais rápido se fizer isso em um arquivo CSV ou JSON do que em um arquivo Parquet; entre outras vantagens que podem deixar sua aplicação com um desempenho melhor ou pior.

Usando um formato binário podemos armazenar os dados de forma mais compacta, o MongoDB por exemplo armazena documentos e usamos JSON, mas internamente ele armazena em arquivo o dado no formato BSON que é uma versão binária do JSON.

Como exemplo da evolução e encoding usando um arquivo no formato binário, o autor comenta sobre o Avro, um formato de encoding binário que começou como um subprojeto do Hadoop. O Avro também usa schema que é fácil de ser lido e escrito (usou o schema do JSON como base para criar seu padrão de schema). Aqui o interessante é você abstrair a ideia para caso no futuro precise usar com outro formato de dados ou criar o seu próprio.

Uma ideia chave do Avro é que não é necessário usar o mesmo schema na leitura e na escrita, ele resolve a leitura dos dados com base na compatibilidade do schema e encontra as colunas pelo nome, quando não encontrar a coluna usa um valor default, isso permite uma fácil evolução do schema ao longo do tempo, podendo ter mais de uma versão do schema.

Outro ponto interessante aqui é qual o schema usado escrita do Avro, quando temos um grande arquivo com milhares de registros, normalmente todos os registros são escritos usando o mesmo schema e esse schema é incluído no inicio do arquivo; no banco de dados vamos inserir os registros ao longo do tempo e o schema pode mudar, então no inicio de cada registros podemos definir a versão do schema usado; para transferir dados Avro via serviço web, o produtor e o consumidor dos dados podem entrar em acordo sobre qual o schema usado nos dados.

Com a versão do schema em cada registro temos a opção de criar um processo de migração dos registros que pode ocorrer a cada nova versão do schema ou conforme a necessidade, por exemplo, caso mude a estrutura interna do valor de alguma coluna.

Talvez você pense, mas eu nunca vou mudar meu banco de dados do A para o B, ou foi feita uma análise para garantir os tipos de dados usados em cada coluna de cada tabela e garantir que não serão alterados no futuro, mas acho que um bom exercício é você pensar na evolução dos seus dados, porque é mais comum do que parece uma aplicação crescer e com isso surgir novas necessidades.

Últimas mudanças simples que ocorreram e que acredito terem alterados diversas aplicações foram: adicionar o 9° dígito no número do telefone celular e o uso do novo padrão de placa do Mercosul, e a partir de 2026 o CNPJ será alfanumérico, em se estiver guardando esse dado em um campo inteiro longo, já pode ir preparando o script de mudança.

No capítulo anterior o autor comenta que o mesmo dado pode ser representado de várias formas entre as camadas da aplicação, e agora vemos que o dado também pode mudar quando é persistido ou transferido:
- seu dado pode ser armazenado no banco de dados que é responsável pela forma como vai codificar e decodificar o dado quando gravar ou ler do disco; 
- depois seus dados podem ser transferidos internamente entre serviços da mesma aplicação ou entre aplicações diferentes, aqui talvez você começou usando um serviço REST transferindo os dados em JSON para facilitar, mas depois percebeu que o consumo da rede ficou grande devido a milhares de trocas de dados entre os serviços e resolveu usar um formato binário na comunicação entre serviços para economizar na transferência dos dados pela rede; 
- talvez você tem um cenário que a comunicação entre serviços pode ser de forma assíncrona usando um sistema de mensageria, porque não há necessidade de aguardar a resposta; porque os dados podem ser previamente acumulados antes de ser processado; porque está fazendo uma solicitação que realmente é lenta e pode ser avisado consultando um status ou recebendo um email quando o processamento terminar; ou qualquer outra regra que necessite de uma implementação assíncrona.

Pode ser que a aplicação que você está desenvolvendo precise de um ou de todos esses modos para manipular os dados, o autor conclui este capítulo lembrando que é necessário tomar cuidado com a compatibilidade ao longo do ciclo de vida do dado.


A seguir o livro começa uma nova parte que aborda alguns desafios relacionados a dados distribuídos, em que o dado não é armazenado em apenas uma única maquina. O capítulo 5 vai tratar o assunto da replicação dos dados que está relacionado com a forma como disponibilizamos o mesmo dado em mais de uma máquina a fim de garantir o acesso rápido, recuperação em caso de desastre, etc.