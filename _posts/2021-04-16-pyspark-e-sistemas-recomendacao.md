---
layout: post
title:  "PySpark e Sistemas de Recomendação"
subtitle: "Criando um pequeno Sistema de Recomendação com PySpark"
date:   2021-04-16
background: '/img/recommender_systems.jpg'
thumbnail: '/img/recommender_systems.jpg'
image_footer: '/img/recommender_systems_thumbnail.jpg'
---
Vivemos em um mundo rodeado por plataformas que nos sugerem o que ouvir, comprar ou assistir. 

Não que isso seja ruim! Muitas vezes assistimos uma série por sugestão do Netflix ou ouvimos uma música porque o Spotify achou que iríamos gostar. Não é verdade?

Já parou para pensar como essas plataformas conseguem fazer isse tipo de sugestão? Não? Sim? Talvez?

No backend de todo esse mundo de sugestões existem os chamados **Sistemas de Recomendação** ou [**_Recommender Systems_**](https://en.wikipedia.org/wiki/Recommender_system). Eles são treinados exatamente para entender dos nossos gostos e desejos, fazendo assim, sugestões de consumo e na maioria das vezes eles acertam em cheio!

Neste post falarei um pouco sobre Sistemas de Recomendação, e mostrarei de uma forma prática, usando Python e PySpark, como esses sistemas funcionam.

<br>
<span style="display:block;text-align:center">
   <img src="/img/recommender_systems_peoples.jpg " alt="" width="500" height="250">
</span>

## Sistemas de Recomendação

<div style="text-align:right;font-size:18px;"> 
  <blockquote>
    A recommender system isn’t only a fancy algorithm. It’s also about understanding the data and your users.
    <br>
    -- <cite>Practical Recommender Systems</cite>
  </blockquote>
</div>

Podemos entender Sistemas de Recomendação somente pelo viés dos algoritmos ou é algo mais enigmático do que imaginamos? Podemos dizer que eles além de possuírem algoritmos complexos também entendem muito sobre os dados que são coletados, e consequentemente conseguem compreender, ou tentar compreender, nosso consumo ao ponto de nos fazer sugestões?

### O que é um Sistema de recomendação?

A imensa quantidade de opções que as grandes plataformas de ecommerce ou streaming possuem em seus catálagos é muito assustador, sem mencionar, a quantidade de acessos que essas mesmas plataformas recebem diariamente. 

Como fazer com que seus usuários fiquem satisfeitos e encontrem exatamente o que procuram? Como auxiliá-los na escolha do que comprar, do que assistir ou ouvir?

Desses tipos de questionamentos surgiram os *Sistemas de Recomendação*. Eles são algoritmos altamente treinados para sugerir opções de consumo dentro do imenso catálago de produtos, conteúdo ou serviços de empresas como Netflix, Amazon, Spotify ou Linkedin. 

Esses sistemas utilizam a imensidade de dados que são coletados e armazenados diariamente para aprender e entender a preferência de consumo dos usuários, oferecendo assim, uma experiência personalizada de utilização de suas plataformas. 

<div style="text-align:right;font-size:18px;"> 
  <blockquote>
    A recommender system calculates and provides relevant content to the user based on knowledge of the user, content, and interactions between the user and the item.
    <br>
    -- <cite>Practical Recommender Systems</cite>
  </blockquote>
</div>

**_Em resumo_**: um Sistema de Recomendação é um sistema que possui algoritmos extremamente especializados e treinados. Ele oferece aos usuários conteúdos e produtos de uma maneira personalizada. 
Para fazer sugestões ele aprende sobre nossos gostos e preferências, usando para isso, uma imensa quantidade de dados coletados pelas interações realizadas dentro de determinada plataforma.

### Algoritmos

Para um Sistema de Recomendação executar sua função é necessário possuir algoritmos "**_inteligentes_**" e bem treinados. 

Nos próximas seções serão descritos os algoritmos mais utilizados na construção de **_SRs_** (Sistemas de Recomendação).

#### Baseado em popularidade

Começamos com um dos tipos mais básicos de algoritmos. Normalmente ele é utilizado para fazer recomendação de produtos ou conteúdos para os usuários, baseados na popularidade de um item.

Para fazer essas recomendações ele se baseia na compra, visualização, curtida ou download que os usuários realizam de determinado produto ou serviço, fazendo assim, um ranking dos items mais populares.

Esse tipo de SR não costuma ser altamente personalizado já que as mesmas recomendações, geralmente, são iguais para vários grupos de usuários.

#### Baseado em conteúdo

Os algoritmos de filtragem baseada em conteúdo fazem recomendações fundamentadas nos conteúdos descritivos (nome, localização, descrição etc) dos items juntamente com as avaliações e comportamento dos usuários, tentando assim, encontrar itens similares em seu catálogo.

Podemos citar aqui, a famosa [**_análise de sentimentos_**](https://en.wikipedia.org/wiki/Sentiment_analysis). O usuário, na maioria das vezes, escreve reviews sobre algum item e o sistema pode se basear nessa review para oferecer ou não produtos similares.

#### Filtragem colaborativa

Esse tipo de algoritmo recomenda itens similares aos quais o usuário curtiu anteriormente. Aqui o item é considerado como um caixa preta, ou seja, o conteúdo descritivo dele não é importante.

A idéia central é calcular o quanto um produto, que o usuário gostou, é similar a outro do catálogo e recomendar somente itens que agradarão ao usuário.

#### Híbridos

Nos dias atuais esse é o tipo de sistema de recomendação mais utilizado. Ele normalmente combina os SR baseado em contéudo e o de filtragem colaborativa. 

Realizar essa mistura de técnicas permite que a acurácia das recomendações sejam muito melhores!

Esses sistemas tornaram-se parte integral de várias plataformas, como o Netflix, para ajudar seus usuários a consumir conteúdo mais acertivo.

#### Curiosidade

O Netflix, em 2009, promoveu uma competição que tinha como objetivo premiar, com 1 milhão de dólares, quem desenvolvesse um *algoritmo de filtragem colaborativa* que fizesse recomendações sem utilizar qualquer informação sobre o usuário ou filme.

O objetivo era se basear somente nas avaliações que os filmes e séries recebiam. Para entender um pouco mais: [Netflix Prize](https://en.wikipedia.org/wiki/Netflix_Prize).

## PySpark

Para criarmos um pequeno Sistema de Recomendação vamos entender primeiro o que é o PySpark!

<br>
<span style="display:block;text-align:center">
   <img src="/img/pyspark_logo.jpeg " alt="" width="500" height="250">
</span>

### O que é Pyspark?

PySpark é uma interface que serve de comunicação com o Spark, e foi desenvolvida utilizando Python. 

**_Em linhas gerais_**: você pode interagir com o Spark usando Python. 

Mas afinal o que é Spark?

#### Spark

[Spark](https://spark.apache.org/) nada mais é do que um framework que possibilita processar grandes quantidade de dados, e é hoje em dia, a ferramenta mais utilizada em Big Data. 

Foi criado pela Universidade da Califórnia, mas atualmente, faz parte do catálago de produtos da Fundação Apache. É open-source e você pode contribuir com o projeto que está disponível no [github](https://github.com/apache/spark).

Todo o core do Spark é escrito em Scala e executado em um JVM (*Java Virtual Machine*). Podemos escrever aplicações Spark em R, Python, Java e Scala. 

Lembram do PySpark? É ele que permite que exista essa comunicação com o core do Spark, caso contrário, teríamos de obrigatoriamente saber Scala para criar aplicações Spark.

#### Biblioteca de Machine Learning

Obviamente o Spark possui uma bliblioteca muito poderosa para trabalharmos com ML (Machine Learning), a [Spark MLlib](https://spark.apache.org/mllib/).

Com essa biblioteca podemos criar modelos de Machine Learning de uma maneira distribuída, e possui a maioria de algoritmos para classificação, regressão, clusterização, sistemas de recomendação e NLP (Natural Language Processing).

O céu é o limite!

## Parte prática: recomendando filmes

Vamos deixar de lado um pouco a teoria e passar à parte prática!

Para mostrar o uso de PySpark e ML para construir um simples Sistemas de Recomendação usaremos um case amplamente disseminado por livros e tutoriais: recomendação de filmes.

É algo que todos gostamos, e que possui grandes datasets disponíveis no [MovieLens](https://grouplens.org/datasets/movielens/).

Quer ver o PySpark em ação? Então diverta-se com um Jupyter Notebook que mostra o funcionamento de um sistema de recomendação, e que se encontra [aqui](https://github.com/pattyvader/recommender_system_notebook/blob/main/movies_ratings.ipynb)!

## Links interessantes

[Practical Recommender Systems](https://www.manning.com/books/practical-recommender-systems)

[Machine Learning with PySpark](https://www.apress.com/gp/book/9781484241301)

[Pro Machine Learning Algorithms](https://www.apress.com/gp/book/9781484235638)

[Collective Intelligence in Action](https://www.manning.com/books/collective-intelligence-in-action)

[PySpark Documentation](https://spark.apache.org/docs/latest/api/python/index.html)

[Recommender Systems: The textbook](https://www.springer.com/gp/book/9783319296579)