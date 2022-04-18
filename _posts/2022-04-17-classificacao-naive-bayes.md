---
layout: post
title: "Classificação com Naive Bayes"
date: 2022-04-17 23:34:00
categories: [ Machine Learning, Classificação ]
tags: [classificação, inteligencia artificial, machine learning, naive bayes]
image: assets/images/posts/2022-04-17-classificacao-nb.png
excerpt: Veja como usar Naive Bayes e calcular as probabilidades das características para realizar uma tarefa de classificação.
featured: true
hidden: true

---

O Naive Bayes é um algoritmo que utiliza o [Teorema de Bayes](https://pt.wikipedia.org/wiki/Teorema_de_Bayes) que descreve a probabilidade de um evento com base nos dados já conhecidos.

Em um cenário de classificação, você pode pensar em um exemplo em que dado uma característica (como: uma nota de avaliação, uma palavra, a característica de um produto, algum atributo relevante) queremos calcular a probabilidade desta característica pertencer a cada uma das classes possíveis, assim classificamos esta característica a partir da classe que possuir a maior probabilidade.

A **Figura 1** apresenta a equação do Teorema de Bayes:

<figure>
    <a href="/assets/images/posts/2022-04-17-classificacao-nb-01.png"><img src="/assets/images/posts/2022-04-17-classificacao-nb-01.png" alt="Teorema de Bayes."></a>
</figure>

Nesta equação temos:

```
P(A | B) = Qual a probabilidade da classe A, dada a característica B?
P(B | A) = Dado a característica B, qual a probabilidade de ser da classe A?
P(A) = Probabilidade da classe A
P(B) = Probabilidade da característica B
```

O uso do Naive Bayes fica um pouco mais interessante quando temos mais de uma característica, neste caso podemos multiplicar a probabilidade de cada uma das características para identificar qual a classe que essas características melhor representam.

A **Figura 2** mostra a equação de Bayes quando aplicada para várias características.

<figure>
    <a href="/assets/images/posts/2022-04-17-classificacao-nb-02.png"><img src="/assets/images/posts/2022-04-17-classificacao-nb-02.png" alt="Multiplicação das características usando o Teorema de Bayes."></a>
</figure>

## Exemplo de como calcular na mão a equação do Teorema de Bayes 

Um exemplo clássico para aprender Naive Bayes é decidir com base em algumas características climáticas se haverá ou não jogo?

**Tabela 1:** mostra os valores de algumas condições climáticas e a classificação (sim ou não) indicando se haverá jogo.

<figure>
    <a href="/assets/images/posts/2022-04-17-classificacao-nb-03.png"><img src="/assets/images/posts/2022-04-17-classificacao-nb-03.png" alt="Tabela de condições climáticas e classificação indicando se haverá ou não jogo."></a>
</figure>

Com base na **Tabela 1** podemos responder a pergunta: dado as características tempo=sol, temperatura=quente, humidade=normal e vento=sim, pode jogar?

Vamos utilizar o Naive Bayes para ajudar a responder essa pergunta, neste tipo de problema temos duas classes possíveis: **sim** e **não**, chamamos de **Classificação Binária**.

> O Naive Bayes também pode ser utilizado para problemas de classificação com mais de duas classes, conhecida como **Classificação multi classes**.

Para cada uma das classes vamos primeiro aprender a calcular na mão as probabilidades com o Naive Bayes e depois vamos ver como implementar um programa que faça a classificação. 

### Exemplo para Sim

Vamos começar calculando a probabilidade para a classe **Sim**. Na **Tabela 2** destaquei em vermelho as características escolhidas e a classe **sim**.

**Tabela 2:** destaca as características informadas na pergunta e a classe **sim**:

<figure>
    <a href="/assets/images/posts/2022-04-17-classificacao-nb-04.png"><img src="/assets/images/posts/2022-04-17-classificacao-nb-04.png" alt="Tabela destaca as característica escolhidas para a classe sim."></a>
</figure>

Seguindo a ordem da equação, vamos começar calculando ```P(B | A)``` que representa as probabilidades de cada característica que pertencem a classe **sim**.

> Obs: aqui vou contar quantas vezes cada uma das características aparecem quando a classe é **sim**, então no caso do **tempo = "sol"**, aparece 2 vezes, entre os 9 registros com a classe **sim**, então sua probabilidade é 2/9; depois seguimos o mesmo processo para as demais características.

```
Tempo = P("sol" | "sim") = 2/9
Temperatura = P("quente" | "sim") = 2/9
Humidade = P("normal" | "sim") = 6/9
Vento = P("sim" | "sim") = 3/9
```

Agora que sabemos individualmente a probabilidade de cada característica para a classe **sim**, vamos multiplicar essas probabilidades. 

```
P(B | "sim") = 2/9 * 2/9 * 6/9 * 3/9 
P(B | "sim") = 0,22 * 0,22 * 0,66 * 0,33
P(B | "sim") = 0,01
```

Podemos dizer que para a classe **sim**, em que dado as características **tempo = sol, temperatura = quente, humidade = normal e vento = sim**, temos a probabilidade de **0,01 ou 1%**.

Agora vamos calcular ```P(A)``` que é a probabilidade de ocorrer a classe **sim**, nos 14 registros que compõem este dataset, temos 9 que correspondem a classe **sim**:

```
P("sim") = 9/14
P("sim") = 0,64
```

Podemos dizer que dentre as amostras que temos disponíveis, temos **64%** dos registros pertencentes a classe **sim**.

E finalmente calculamos ```P(B)``` que são as probabilidades de cada característica no conjunto todo:

```
Tempo = P("sol") = 5/14
Temperatura = P("quente") = 4/14
Humidade = P("normal") = 7/14
Vento = P("sim") = 6/14

P(B) = 5/14 * 4/14 * 7/14 * 6/14
P(B) = 0,35 * 0,28 * 0,5 * 0,42
P(B) = 0,02
```

Então, dado as características **tempo = sol, temperatura = quente, humidade = normal e vento = sim**, temos uma probabilidade de **2%**.

Agora que já calculamos cada parte da equação, vamos colocar tudo na equação:

```
P(A | B) = (0,01 * 0,64) / 0,02
P(A | B) = 0,32
```

Temos como resultado uma probabilidade de **32%** para a classe **sim** que ocorra o jogo dado as características: **tempo=sol, temperatura=quente, humidade=normal e vento=sim**.

> Note como as características tempo e temperatura foram as que mais contribuíram para que a probabilidade fique baixa.

### Exemplo para Não

Vamos fazer o cálculo das probabilidades para a classe **não**.

**Tabela 3:** destaca as características informadas na pergunta e a classe **não**:

<figure>
    <a href="/assets/images/posts/2022-04-17-classificacao-nb-05.png"><img src="/assets/images/posts/2022-04-17-classificacao-nb-05.png" alt="Tabela destaca as característica escolhidas para a classe não."></a>
</figure>

Novamente vamos começar calculando ```P(B | A)``` que agora representa as probabilidades de cada característica que pertencem a classe **não**:

```
Tempo = P("sol" | "não") = 3/5
Temperatura = P("quente" | "não") = 2/5
Humidade = P("normal" | "não") = 1/5
Vento = P("sim" | "não") = 3/5
```

E vamos multiplicar todas as probabilidades:

```
P(B | "não") = 3/5 * 2/5 * 1/5 * 3/5
P(B | "não") = 0,6 * 0,4 * 0,2 * 0,6
P(B | "não") = 0,02
```

Podemos dizer que para a classe **não**, em que dado as características **tempo = sol, temperatura = quente, humidade = normal e vento = sim**, temos a probabilidade de **0,02 ou 2%**.

Agora vamos calcular ```P(A)``` que é a probabilidade de ocorrer a classe **não**:

```
P("não") = 5/14
P("não") = 0,35
```

Podemos dizer que dentre as amostras que temos disponíveis, temos **35%** dos registros pertencentes a classe **não**.

Como já calculamos ```P(B)``` que são as probabilidades de cada característica no conjunto todo e não mudam de acordo com a classe, vamos utilizar o resultado já calculado:

```
P(B) = 0,02
```

Agora vamos colocar tudo na equação:

```
P(A | B) = (0,02 * 0,35) / 0,02
P(A | B) = 0,35
```

Apesar da pouca diferença, temos **35%** de probabilidade para a classe **não**, então dado as características **tempo=sol, temperatura=quente, humidade=normal e vento=sim**, a resposta fornecida pelo Naive Bayes é que **não** haverá jogo.


## Utilizando Scikit-Learn para classificar com Naive Bayes

Agora vamos criar um modelo que usa o Naive Bayes para realizar uma classificação. Neste exemplo vou usar o mesmo dataset apresentado no exemplo anterior, então com base nas características de tempo, temperatura, humidade e vento, queremos classificar se haverá ou não jogo.

Você pode copiar as informações da **Tabela 1** ou usar este arquivo [jogar.csv](https://drive.google.com/file/d/1iSn8NQEYJLrsYuZrT30C4M8rKOcsJ9od/view?usp=sharing).

Começamos lendo o arquivo, aqui vou utilizar o Pandas para ler o arquivo e gerar um DataFrame, também já mostro as primeiras linhas do arquivo:

``` python
import pandas as pd

df = pd.read_csv('jogar.csv')
df.head()
```

Todas as características que temos neste dataset são categóricas, então vamos começar a manipulação dos dados convertendo os dados para numérico. O LabelEncoder do Scikit Learn faz isso de modo bem simples.

A seguir criamos um LabelEncoder e geramos um Array (como se fosse uma nova coluna com a versão numérica) de cada característica:

``` python
from sklearn import preprocessing

tempo_le = preprocessing.LabelEncoder()
tempo = tempo_le.fit_transform(df['tempo'].values)

print("Categorias encontradas para tempo: {}".format(tempo_le.classes_))
print("Exemplo de encode de tempo: {} e seu valor real: {}".format(tempo[0], tempo_le.inverse_transform([tempo[0]])))

temperatura_le = preprocessing.LabelEncoder()
temperatura = temperatura_le.fit_transform(df['temperatura'].values)

print("\nCategorias encontradas para temperatura: {}".format(temperatura_le.classes_))
print("Exemplo de encode de temperatura: {} e seu valor real: {}".format(temperatura[0], temperatura_le.inverse_transform([temperatura[0]])))

humidade_le = preprocessing.LabelEncoder()
humidade = humidade_le.fit_transform(df['humidade'].values)

print("\nCategorias encontradas para humidade: {}".format(humidade_le.classes_))
print("Exemplo de encode de humidade: {} e seu valor real: {}".format(humidade[0], humidade_le.inverse_transform([humidade[0]])))

vento_le = preprocessing.LabelEncoder()
vento = vento_le.fit_transform(df['vento'].values)

print("\nCategorias encontradas para vento: {}".format(vento_le.classes_))
print("Exemplo de encode de vento: {} e seu valor real: {}".format(vento[0], vento_le.inverse_transform([vento[0]])))
```

Temos como resultado:

``` python
Categorias encontradas para tempo: ['chuva' 'nublado' 'sol']
Exemplo de encode de tempo: 2 e seu valor real: ['sol']

Categorias encontradas para temperatura: ['frio' 'quente' 'suave']
Exemplo de encode de temperatura: 1 e seu valor real: ['quente']

Categorias encontradas para humidade: ['alta' 'normal']
Exemplo de encode de humidade: 0 e seu valor real: ['alta']

Categorias encontradas para vento: ['não' 'sim']
Exemplo de encode de vento: 0 e seu valor real: ['não']
```

Agora vamos criar um novo DataFrame contendo a versão numérica de cada uma das características:

``` python
jogar_df = pd.DataFrame()
jogar_df['tempo'] = tempo
jogar_df['temperatura'] = temperatura
jogar_df['humidade'] = humidade
jogar_df['vento'] = vento

X = jogar_df.values
print(X)
```

**X** é o novo DataFrame após o encode de cada uma das características. Este DataFrame será utilizado como entrada para treinar o modelo do Naive Bayes:

``` python
[[2 1 0 0]
 [2 1 0 1]
 [1 1 0 0]
 [0 2 0 0]
 [0 0 1 0]
 [0 0 1 1]
 [1 0 1 1]
 [2 2 0 0]
 [2 0 1 0]
 [0 2 1 0]
 [2 2 1 1]
 [1 2 0 1]
 [1 1 1 0]
 [0 2 0 1]]
```

Vamos fazer o mesmo processo para transformar a coluna **jogar** da classe:

``` python
jogar_le = preprocessing.LabelEncoder()
y = jogar_le.fit_transform(df['jogar'].values)

print(y)

print("Categorias encontradas para jogar: {}".format(jogar_le.classes_))
print("Exemplo de encode de jogar: {} e seu valor real: {}".format(y[0], jogar_le.inverse_transform([y[0]])))

[0 0 1 1 1 0 1 0 1 1 1 1 1 0]
Categorias encontradas para jogar: ['não' 'sim']
Exemplo de encode de jogar: 0 e seu valor real: ['não']
```

Agora **y** é o vetor com os valores representando as classes **sim** e **não**.

No Scikit Learn temos uma implementação para o Naive Bayes, vamos treinar o modelo:

``` python
# Documentação do Naive Bayes: https://scikit-learn.org/stable/modules/naive_bayes.html
from sklearn.naive_bayes import CategoricalNB

nb = CategoricalNB()
nb.fit(X, y)
```

A função **fit** vai treinar o Naive Bayes, e agora o **nb** representa o modelo treinado.

Com base na pergunta inicial, dado as características **tempo=sol, temperatura=quente, humidade=normal e vento=sim**:

``` python
nova_amostra = [
      tempo_le.transform(['sol'])[0], 
      temperatura_le.transform(['quente'])[0],
      humidade_le.transform(['normal'])[0],
      vento_le.transform(['sim'])[0]
  ]

print(nova_amostra)

[2, 1, 1, 1]
```

Podemos usar o **nb** para predizer a classificação:

``` python
saida = nb.predict([nova_amostra])
print(jogar_le.inverse_transform(saida))
```

E temos também a classificação:

``` python
['não']
```
