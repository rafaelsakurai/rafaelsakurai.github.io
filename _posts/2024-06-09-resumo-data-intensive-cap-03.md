---
layout: post
title: "Resumo do capítulo 3 do livro Designing Data-Intensive Applications"
date: 2024-06-09 17:35:00
categories: [ Livro, Designing Data-Intensive Applications ]
tags: [livro, estrutura de dados, hash index, LSM-Tree, SSTable, OLAP, OLTP, armazenamento orientado a coluna, materialized view]
excerpt: Neste capítulo o autor aborda como os dados são armazenados e recuperados, como construir um index para melhorar a recuperação das informação, diferença entre OLAP e OLTP, armazenamento orientado a colunas e materialized view.
featured: false
hidden: true
---

No [capítulo 2](https://www.sakurai.dev.br/resumo-data-intensive-cap-02) o livro aborda o tema dos modelos de dados e linguagens de consulta, comentando sobre como o dado é transformado entre as camadas da aplicação, os diversos tipos de banco de dados e linguagens para consultar os dados.


## Capítulo 3 - Armazenar e recuperar os dados

O autor começa este terceiro capítulo questionando o porquê um desenvolvedor deveria saber como o banco de dados faz internamente para armazenar e recuperar os dados?

E a resposta é bem simples, precisa saber como o banco de dados funciona internamente para poder usar o melhor desempenho dele. Se você está mandando gravar dados o que poderia ser a causa de uma lentidão ou se você está consultando dados, mas demora um tempo enorme, o que é que está acontecendo, ou melhor, por onde você deve começar sua investigação para obter um melhor desempenho? Se você não conhece o funcionamento do banco de dados, não saberá nem por onde começar.

### Estrutura de dados

Para começar a explicação, o autor mostra o código de um simples Bash Script que possui as funções para adicionar (concatenando) texto (no formato chave e valor) no final de um arquivo e obter o conteúdo do texto procurando por sua chave.

O arquivo que mantém todos os registros é um simples arquivo de log (no qual você simplesmente adicionar mais dados no final), a função de adicionar possui um bom desempenho, porque tem que simplesmente adicionar dados no final do arquivo, mas a função para obter um registro com base na sua chave precisa percorrer o arquivo procurando a chave até encontrar ou chegar no final do arquivo representando um custo que é em termos de complexidade do algoritmo O(n), isso significa que quanto maior o tamanho do n (quantidade de registros no arquivo), maior será o tempo médio para pesquisar uma chave dentro do arquivo.

Agora temos um problema para melhorar, o desempenho na consulta, uma forma de resolver isso é usando uma estrutura de dados para criar um **índice** que servirá para indicar onde estão os registros no seu arquivo de log. O índice é uma estrutura derivada dos dados primários e podemos ter diversos índices criados caso tenhamos diversas formas de consultar os dados.

O índice é um trade-off, enquanto ele deixa a escrita um pouco mais lenta porque precisará inserir os dados no arquivo de log e também registrar no índice, ele deixará a consulta mais rápida porque encontrará os registros pesquisados de forma mais otimizada. Então não é simplesmente colocando índice em todos as colunas de uma tabela que você resolverá todos os problemas de desempenho das consultas, pode ser que você ganhe outros problemas, como a falta espaço interno para armazenar todos os índices, não conseguir manter um índice em memória por falta de espaço, lentidão na escrita, etc.

#### Hash index

O índice usando o Hash Map ou Hash Table (estrutura similar ao Dicionário em algumas linguagens de programação) permite mapear em memória um conjunto de chaves (id da tabela) para a posição correspondente do seu valor no arquivo de log.

Como o arquivo de log sempre faz um append no final (até agora no exemplo não temos um mecanismo para atualizar os registros do log), podemos ter chaves duplicadas com novos valores, este arquivo crescerá ficando lento a sua manipulação (abrir o arquivo, adicionar mais um par de chave/valor e fechar o arquivo) e ocupando muito espaço em disco. Para resolver isso o arquivo de log pode ser dividido em segmentos menores, em que cada segmento pode ser compactado, mantendo apenas o último registro de cada chave e depois feito o merge (união dos registros mantendo sempre o valor mais recente para cada chave) destes arquivos de segmentos.

Importante é que os arquivos de segmentos não são alterados depois que são criados e sempre que ocorre o merge entre segmentos sempre é gerado um novo arquivo. O merge dos arquivos pode ocorrer em background e enquanto estiver sendo feito podemos continuar lendo os segmentos antigos, enquanto que todas as novas escritas são feitas no último arquivo do segmento, e assim que o merge terminar o novo arquivo será usado e os arquivos antigos podem ser apagados.

Cada segmento agora terá seu próprio índice de hash table na memória e para encontrar o valor mais recente de uma chave podemos consultar primeiro os índices dos arquivos de segmentos mais recentes e depois os mais antigos.

Agora o autor cita alguns problemas que são importantes e que precisam ser tratados pelas aplicações reais:

**Formato do arquivo:** como os dados serão armazenados internamente, um arquivo CSV ou JSON não são formatos apropriados, porque ocupam muito espaço em disco, é mais rápido e simples usar um formato binário para armazenar os dados;
**Remover os registros:** a forma mais simples de permitir remover registros de um arquivo de log é adicionando um novo registro com uma marcação para remover, assim ele não será carregado no índice e quando ocorrer o próximo merge dos arquivos de segmento este registros não será incluído no novo arquivo de segmento;
**Recuperar em caso de desastre:** se o banco de dados cair todos os hash tables dos segmentos que estavam na memória serão perdidos e precisará recriá-los em memória, uma forma de agilizar esse processo é guardar em disco uma foto (snapshot) do estado de cada hash table para que ele possa ser carregado de forma mais rápida;
**Registros escritos parcialmente:** se o banco de dados cair no meio da escrita pode gravar algum registro incompleto, uma forma de garantir que o registro está inteiro é incluir um checksum que serve para validar se os registros estão corretos e caso contrário conseguir ignorar a parte que estiver corrompida do arquivo;
**Controle de concorrência:** com o uso dos arquivos de segmentos suportando apenas a operação de append, podemos ter uma thread escrevendo e diversas threads lendo os dados.

Quando esses problemas são devidamente tratados conseguimos reduzir o volume armazenado em disco, reduzir o tempo de processamento dos dados, manter o dado consistente e recuperar em caso de falha.

O Hash Table tem suas limitações, ele precisa caber na memória e ele não é eficiente quando queremos fazer consultas com paginação que pegam um intervalo de chaves.

Como forma de melhorar a implementação do Banco de Dados e não ter as limitações de um Hash Table, o autor comenta de duas estruturas encontradas nos bancos de dados, SSTable (Sorted String Table) e LSM-Tree (Log-Structured Merge-Tree).

No Hash Table os registros são organizados em chave e valor, no qual a chave pode ser por exemplo o id do seu registro e o valor será a posição deste registro no arquivo de log, mas não possui um dado ordenado, então a pesquisa por uma chave acaba usando um índice com todas as chaves em memória.

O SSTable é ordenado pela chave e pode ser implementado usando B-Trees (árvores binárias) como o red-black trees ou AVL trees que são estruturas de árvores balanceadas, isso traz algumas vantagens como: 1- fazer o merge mais eficientes dos segmentos; 2- a pesquisa de uma chave também é mais eficiente, não sendo necessário percorrer todas as chaves e também não precisando manter um índice com todas as chaves em memória, você pode manter um conjunto esparso de chaves e memórias e identificar o intervalo que está a chave que você está procurando; 3- compressão dos blocos de registros economizando disco e transferência de dados.

A desvantagem é que agora não poderá ser um simples append-only, precisará saber onde deverá inserir cada nova chave. O problema desta abordagem é que as novas escritas serão perdidas caso ocorra uma falha na máquina, mas pode ser evitado usando um arquivo de log separado que suporta apenas append para ser adicionado cada novo registro nele e com base neste arquivo restaurado toda a SSTable em memória.

Outra estrutura de index é o LSM-Tree que tem a vantagem de ser mais rápido na escrita do que o B-Tree, isso porque a B-Tree escreve pelo menos duas vezes cada registro (na árvore da página e no arquivo de log), enquanto que o LSM-Tree escrever arquivos sequenciais de SSTable de forma compacta e também economiza no espaço em disco.

Há também uma escolha que impacta muito a performance se o dado fica armazenado em disco ou memória. O disco tem a vantagem de ter dados duráveis, enquanto na memória podemos perder caso a máquina caia, mas a memória é muito mais rápida e pode ser usada como cache.

As vezes é necessário usar uma estratégia mista, mantendo alguns datasets em disco e outros em memória.


### Processamento Transacional ou Analytics

Agora o autor comenta um ponto super interessante que é qual o objetivo de uso dos dados armazenados e isso engloba dois grandes assuntos: base de dados para processar transações (OLTP) e base de dados para analytics (OLAP).

No OLTP temos um negócio que parte de algumas premissas como o dado precisar de controle de transação e precisa das garantias que um banco de dados que suporta ACID (atomicidade, consistência, isolamento e durabilidade), neste necessário não é permitido perda de registros, normalmente usado para controle de vendas, transações bancárias, autenticação dos usuários, etc.

No uso do OLAP você quer ler um grande volume de dados, executar agregações e gerar dashboards. Como o volume de dados é muito grande a falta de alguns registros provavelmente não impactam o uso dos dados, imagine a quantidade de visualizações em páginas de um grande portal de notícias, todas as pesquisas realizadas em um site de busca, dependendo do uso final após agregar os dados, pode ser que a falta de alguns registros não mude os ponteiros de um gráfico no dashboard. Então nestes cenários o processamento e armazenamento do grande volume de dados ganham mais prioridade, porque independente do grande volume, ninguém quer ficar esperando horas para aguardar um gráfico ser atualizado.

Bancos de dados tendem a focar mais no lado do OLTP e os DIAS normalmente são relutantes em deixar executar consultas analíticas no DB porque são custosas e precisam ler grandes volumes de dados reduzindo a performance das transações em execução.

Já vi pessoas derrubarem o banco de dados devido ao grande volume consultado, já vi bancos de dados configurados para interromper automaticamente consultas com grande complexidade, e nessas horas que você aproveita para explorar novas formas de solucionar o problema e aprender mais, por exemplo já deixando previamente agrupado parte dos dados, dividindo o dado em partes e instâncias diferentes de acordo com alguma participação, etc.

Os sistemas de Data Warehouse tendem a resolver os problemas relacionados à OLAP, porque recebem dados de diversos sistemas transacionais e modelam esses dados para facilitar a análise em cima do volume.

Aqui entramos em uma outra área Interessante que é a forma como os dados são armazenados para facilitar seu uso futuro, como duplicando alguns dados para evitar joins ou separando os dados a fim de evitar duplicar e ter menos volume.


### Armazenamento orientado a colunas

Conforme os dados vão aumentando para trilhões de registros e petabytes de dados, começamos a ter problemas de performance para armazenar e consultar, mas nem sempre precisamos de todas as colunas para gerar alguma informação.

A ideia do armazenamento orientado a colunas não é armazenar todos os valores de um registro juntos, mas sim armazenar todos os valores por coluna. Os dados das colunas são salvos na ordem mesma dos registros, então é possível pegar o valor de uma posição para todas as colunas e recompor um registro, mas o grande benefício é consultar apenas as colunas necessárias, assim poderá ler menos dados do que ler os registros inteiros e não usar todos os valores lidos.

Exemplo: o formato de arquivo Parquet é colunar e compactado, então se você comparar um arquivo com os registros salvos em JSON, CSV e Parquet, você verá que o arquivo Parquet ocupa menos espaço em disco e se você for ler esses arquivos para processar o dados armazenados em algumas colunas também terá uma performance muito melhor no Parquet (essa parte será abordada no próximo capítulo).

O BigQuery também é colunar então tendemos a economizar no valor de bytes processados se nas consultas informarmos os nomes das colunas ao invés de usar o * para obter todas as colunas, mesmo aquelas não utilizadas na consulta.

O uso da base ou arquivo colunar é interessante quando não há necessidade de ordenar os registros, um exemplo é se você precisa processar dados organizados ou particionado por alguma informação que serve de filtro e agrupados, assim você pode ler apenas os arquivos necessário e apenas as colunas necessárias para gerar alguma informação.


### Materialized Views

Um último ponto mencionado neste capítulo são as view materializadas, porque ao invés de consultar sempre no dado original, podemos gerar views que ficam armazenadas em cache em disco ou na memória para aumentar a performance no seu próximo uso.

No Spark por exemplo, conseguimos manipular os dados e guardá-los em cache, podemos até deixar o Spark controlar se é melhor manter o cache em disco ou memória, mas a principal vantagem disso é se você utiliza o mesmo dado pré-processado em cache em diversos momentos do seu Pipeline, assim evitando o reprocessamento destes dados e possivelmente até a releitura deles.


Para concluir, o interessante deste capítulo foi a abordagem para mostrar como os bancos de dados lidam com o problema da crescente dos dados usando index e como isso é implementado utilizando a estrutura de dados de árvores. É importante entender como temos soluções diferentes dependendo se o dados tem como objetivo final ser usado por uma ferramenta transacional ou analítica, e como podemos melhorar o uso dos dados na medida que continuam crescendo por meio de armazenamento colunar ou usando views materializadas como cache.


O [capítulo 4](https://www.sakurai.dev.br/resumo-data-intensive-cap-04) aborda o conceito de códificação e como o formato que salvamos os dados em disco influência para ler, escrever e também o espaço de armazenamento em disco, e também aborda a importância do esquema dado que podemos ter arquivos com dados que serão estruturados e não estruturados.