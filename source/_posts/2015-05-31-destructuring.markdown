---
layout: post
title: "Destructuring"
date: 2015-05-31 10:59:23 -0300
comments: true
categories: clojure fp
---

*Destructuring* é uma das features mais legais do Clojure, permitindo mapear valores individuais de um vetor ou mapa para variáveis, deixando seu código mais legível e conciso. <!-- more -->

O exemplo mais simples é mapear valores de um vetor para variáveis independentes:

{% gist eb30be9436bcbd7233f0 %}

No exemplo acima estamos mapeameando todos os valores do vetor passado, mas é possível ignorar alguns valores e pegar só os valores que você deseja:

{% gist 6f882f2a462351c967e6 %}

Caso queira ignorar um valor em uma posição específica, nomeie a variável com `_`, uma convenção adotada na maioria dos casos:

{% gist 89d59ad1da867f3dc412 %}

Quando criamos funções recursivas é muito comum precisarmos separar um vetor em `head` e `tail`. Utilizando destructuring a tarefa se torna muito simples:

{% gist f6df849adffa3c6c7efc %}

Se você precisar acessar o vetor com todos os elementos, basta usar a diretiva `:as`. Um *bind* será feito para o vetor:

{% gist 370147f21b2f4ed3018b %}

Podemos usar *destructuring* com mapas, o que muda é que agora será necessário passar a `key`, além do *local name*:

{% gist 3c70b36973e8d84c0d28 %}

Nada impede que você criei os *locals* usando o mesmo nome das *keys*:

{% gist b24435fe1de47dd6ff82 %}

Mas perceba que é meio redundante declarar *locals* com os mesmos nomes das `keys`. Pra evitar essa repetição, podemos utilizar a diretiva `:keys` que permite você passar as *keys* que você quer disponível:

{% gist aef5bfd30f5bba293804 %}

Em muitos casos não temos certeza que o mapa passado como parâmetro terá as chaves que esperamos, podendo nos causar dores de cabeça, lidando com valores `nil`. O normal nestes casos é atribuir um valor *default*, programando defensivamente:

{% gist 44a70bad685f30d54949 %}

O interessante ao usar *destructuring* com mapas, é que podemos extrair valores *nested*, ou seja, valores de mapas dentro de mapas:

{% gist 5cd23394a563c5b9168a %}

Você pode ver mais detalhes sobre o uso de *destructuring* na [documentação](http://clojure.org/special_forms).

Por último, gostaria de ressaltar que você pode usar *destructuring* para fazer *binding* dentro do *let*, como foi feitos nos exemplos, e também em funções.

