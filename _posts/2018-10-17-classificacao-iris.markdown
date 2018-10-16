---
layout: post
title: "Decision Tree: Aprendendo a classificar tipos de flor Iris"
date: 2018-10-17 08:00:00
tags: [introducao, aprendizado, maquinas, supervisionado, não supervisionado, reforço]
published: true
excerpt: Este post explica como funciona uma Decision Tree (Árvore de Decisão) e como utilizar sua implementação feita no scikit-learn.
comments: true
image: 2018-10-17-classificacao-iris-02.png
---

O dataset de flores do tipo Iris é um dos mais básicos utilizado para ensinar como funciona um modelo de classificação.

# Como podemos classificar uma flor?

O dataset de Iris é clássico e muito utilizado para ensinar como funciona modelos de classificação. A ideia principal é com base nas caracteristicas da flor, classificar (definir a classe / rótulo) qual é o tipo de Iris.

A **Figura 1** apresenta os três tipos da flor Iris: Versicolor, Setosa e Virginica:

\\
**Figura 1:** Tipos de flores Iris.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-01.png"><img src="/images/posts/2018-10-17-classificacao-iris-01.png" alt="Os três tipos da flor Iris: Versicolor, Setosa e Virginica."></a>
</figure>

**Fonte:** [https://www.datacamp.com/community/tutorials/machine-learning-in-r](https://www.datacamp.com/community/tutorials/machine-learning-in-r)

\\
Este dataset é composto por cinco características: SepalLength (Comprimento da Sépala), SepalWidth (Largura da Sépala), PetalLength (Comprimento da Pétala), PetalWidth (Largura da Pétala) e class (Classe).

A **Figura 2** mostra visualmente as características da flor Iris:

\\
**Figura 2:** Características da flor Iris.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-03.png"><img src="/images/posts/2018-10-17-classificacao-iris-03.png" alt="características da flor Iris."></a>
</figure>

No UCI Machine Learning Repository tem vários conjuntos de dados diferentes e um deles é o [Iris Dataset](https://archive.ics.uci.edu/ml/datasets/Iris) composto por 150 amostras. A seguir são apresentados alguns exemplos do dataset:

|SepalLength|SepalWidth|PetalLength|PetalWidth|Class|
|:---|:---|:---|:---|:---|
|5.1 | 3.5 | 1.4 | 0.2 | Iris-setosa     |
|5.0 | 3.3 | 1.4 | 0.2 | Iris-setosa     |
|7.0 | 3.2 | 4.7 | 1.4 | Iris-versicolor |
|5.7 | 2.8 | 4.1 | 1.3 | Iris-versicolor |
|6.3 | 3.3 | 6.0 | 2.5 | Iris-virginica  |
|5.9 | 3.0 | 5.1 | 1.8 | Iris-virginica  |

# Árvore de decisão

A Árvore de Decisão (Decision Tree) é um modelo que cria uma árvore com a representação das possíveis decisões que podem ser tomadas para classificar os dados. A **Figura 3** apresenta uma representação da árvore de decisão:

\\
**Figura 3:** Representação da Árvore de Decisão.

<center>
<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-04.png"><img src="/images/posts/2018-10-17-classificacao-iris-04.png" alt="Árvore de Decisão."></a>
</figure>
</center>

Cada Nó representa uma decisão e cada Folha representa o valor que será classificado uma nova amostra.

Pela organização em árvore é fácil de interpretar e entender seu funcionamento.

No exemplo das flores Íris, teremos três classes diferentes que serão identificadas nas folhas da árvore. Após treinar o modelo teremos uma representação similar ao apresentado na **Figura 4**:

\\
**Figura 4:** Árvore de Decisão treinada com o dataset de flores Iris.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-05.png"><img src="/images/posts/2018-10-17-classificacao-iris-05.png" alt="Árvore de Decisão treinada com o dataset de flores Iris."></a>
</figure>

O primeiro Nó decide que se a característica **PetalLenght for menor ou igual a 2.45**, então deve seguir para o lado direito da árvore, caso contrário segue para o lado esquerdo da árvore.

Então, com uma nova amostra com as características:

|SepalLength|SepalWidth|PetalLength|PetalWidth|
|:---|:---|:---|:---|
|5.0 | 3.6 | 1.6 | 0.5 |

Podemos facilmente percorrer a árvore para identificar a classe com base nessas característica, como mostrado na **Figura 5**. Nesse exemplo podemos classificar essa flor como uma Iris-setosa.

\\
**Figura 5:** Percorrendo o dataset para encontrar a classe Iris-setosa.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-06.png"><img src="/images/posts/2018-10-17-classificacao-iris-06.png" alt="Percorrendo a Árvore de Decisão para encontrar uma classificação como Iris-setosa."></a>
</figure>

Uma nova amostra com as características:

|SepalLength|SepalWidth|PetalLength|PetalWidth|
|:---|:---|:---|:---|
|5.8 | 2.7 | 4.2 | 1.2 |

Podemos classificar essa flor como uma Iris-versicolor.

\\
**Figura 6:** Percorrendo o dataset para encontrar a classe Iris-versicolor, como mostrado na **Figura 6**.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-07.png"><img src="/images/posts/2018-10-17-classificacao-iris-07.png" alt="Percorrendo a Árvore de Decisão para encontrar uma classificação como Iris-versicolor."></a>
</figure>

Uma nova amostra com as características:

|SepalLength|SepalWidth|PetalLength|PetalWidth|
|:---|:---|:---|:---|
|7.0 | 3.2 | 5.2 | 2.4 |

Podemos classificar essa flor como uma Iris-virginica, como mostrado na **Figura 7**.

\\
**Figura 7:** Percorrendo o dataset para encontrar a classe Iris-virginica.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-08.png"><img src="/images/posts/2018-10-17-classificacao-iris-08.png" alt="Percorrendo a Árvore de Decisão para encontrar uma classificação como Iris-virginica."></a>
</figure>

# Entendendo como funciona a Árvore de Decisão

## Entropia

Para montar uma Árvore de Decisão é necessário saber quais valores separam melhor os dados, e a entropia é utilizada para calcular a incerteza dos dados, então este valor representa a quantidade das amostras das várias classes.

Quando mais próximo for o valor de zero, menor será a variação das classes, portanto teremos mais amostras da mesma classe, e o contrário indica que os dados possuem amostras de várias classes.

A entropia dos dados é calculada com a **Equação 1**:

\\
**Equação 1:** Equação da Entropia.

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-09.png"><img src="/images/posts/2018-10-17-classificacao-iris-09.png" alt="Equação da Entropia."></a>
</figure>

Cada **p** representa a probabilidade de uma amostra ser de uma determinada classe.

No primeiro Nó da árvore temos a entropia de 1.585, que representa uma entropia alta, porque neste momento temos amostra de dados das três classes de Iris.

\\
**Figura 8:** Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris.

<center>
<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-10.png"><img src="/images/posts/2018-10-17-classificacao-iris-10.png" alt="Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris."></a>
</figure>
</center>

Para calcular a entropia, primeiro calculamos a probabilidade de cada classe. Dado que temos 150 amostras e 50 de cada classe, as probabilidades são:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-13.png"><img src="/images/posts/2018-10-17-classificacao-iris-13.png" alt="Calculo da probabilidade para cada classe."></a>
</figure>

Depois calculamos o valor da entropia:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-14.png"><img src="/images/posts/2018-10-17-classificacao-iris-14.png" alt="Calculo da entropia."></a>
</figure>

Na primeira folha da árvore temos a entropia de 0.0, que representa uma entropia baixa, porque neste momento temos amostra de apenas uma classe.

\\
**Figura 9:** Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris.

<center>
<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-11.png"><img src="/images/posts/2018-10-17-classificacao-iris-11.png" alt="Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris."></a>
</figure>
</center>

Calculamos a probabilidade de cada classe. Dado que temos 50 amostras e todas são da classe iris setosa, as probabilidades são:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-15.png"><img src="/images/posts/2018-10-17-classificacao-iris-15.png" alt="Calculo da probabilidade para cada classe."></a>
</figure>

Depois calculamos o valor da entropia:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-16.png"><img src="/images/posts/2018-10-17-classificacao-iris-16.png" alt="Calculo da entropia."></a>
</figure>

No segundo Nó da árvore temos a entropia de 1.0, que representa uma entropia alta, porque neste momento temos amostra de duas classes.

\\
**Figura 10:** Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris.

<center>
<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-12.png"><img src="/images/posts/2018-10-17-classificacao-iris-12.png" alt="Primeiro nó da Árvore de Decisão treinada com o dataset de flores Iris."></a>
</figure>
</center>

Calculamos a probabilidade de cada classe. Dado que temos 100 amostras e 50 são da classe  iris versicolor e 50 são da classe iris virginica, as probabilidades são:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-16.png"><img src="/images/posts/2018-10-17-classificacao-iris-16.png" alt="Calculo da probabilidade para cada classe."></a>
</figure>

Depois calculamos o valor da entropia:

<figure>
    <a href="/images/posts/2018-10-17-classificacao-iris-17.png"><img src="/images/posts/2018-10-17-classificacao-iris-17.png" alt="Calculo da entropia."></a>
</figure>

E assim por diante, até chegar no nível de profundidade máxima da árvore.

## Implementando o calculo da entropia

Para calcular a entropia, podemos criar uma função no Python que implementa sua equação:

{% highlight python %}
def entropia(probabilidades):
	return sum(- p * math.log(p, 2) for p in probabilidades if p)
{% endhighlight %}

Essa função recebe as probabilidades das classes e devolve o valor da entropia:

{% highlight python %}
p1 = 50.0/150.0
p2 = 50.0/150.0
p3 = 50.0/150.0
print entropia([p1, p2, p3])
{% endhighlight %}

# Usando Pandas e Scikit-learn para treinar uma árvore de decisão

No Python, temos várias bibliotecas implementadas que facilitam o uso de modelos de aprendizado de máquinas:

## Carregando o Dataset de Iris

Com o pandas podemos carregar facilmente os dados a partir de um dataset como um arquivo CSV. Você pode fazer o download do dataset de [Iris Dataset](https://archive.ics.uci.edu/ml/datasets/Iris) e baixar apenas o arquivo **iris.data**.

{% highlight python %}
import pandas as pd
atributos = ['SepalLength', 'SepalWidth', 'PetalLength', 'PetalWidth', 'Class']
df = pd.read_csv('iris.data', names=atributos)

# Define uma matriz as caracteristicas que representam uma flor Iris.
X = df[df.columns.difference(['Class'])].values

# Define um vetor com das classes de cada uma das flores.
y = df['Class'].values
{% endhighlight %}

## Treinando uma Árvore de Decisão

{% highlight python %}
from sklearn.tree import DecisionTreeClassifier

iris_classificador = DecisionTreeClassifier(random_state=1234, criterion='entropy', max_depth=3)

# A função fit é usada para treinar o modelo, com base nas entradas X
# ('SepalLength', 'SepalWidth', 'PetalLength', 'PetalWidth') deve
# aprender a classificar y ('Class').
iris_classificador.fit(X, y)
{% endhighlight %}

## Predizendo novas amostras

{% highlight python %}
novos_exemplos = [[1.6,0.5,5.0,3.6], [4.2,1.2,5.8,2.7], [5.2,2.4,7.0,3.2]]

# Função predict tenta classificar novos exemplos de amostras.
print iris_classificador.predict(novos_exemplos)
{% endhighlight %}

## Avaliando a Árvore de Decisão com validação cruzada

{% highlight python %}
from sklearn.model_selection import cross_val_score
avaliacao = cross_val_score(iris_classificador, X, y, scoring='accuracy', cv=5)
print avaliacao.mean()
{% endhighlight %}