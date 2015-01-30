---
layout: post
title: "Aumente o poder das suas macros usando Unquote Splicing"
date: 2015-01-09 00:18:50 -0200
comments: true
categories: clojure fp
---

No post anterior, aprendemos a criar *macros* que aumentam significativamente a legibilidade do nosso código. Porém, deixamos um detalhe importante para trás: *Unquote Splicing* <!-- more -->

No final do [último post](http://lucasas.github.io/blog/2014/12/19/criando-macros-usando-syntax-quoting) criamos uma *macro* chamada `unless`, que basicamente executa múltiplas expressões caso uma determinada condição seja `false`. 

{% gist afddd9cca1f57dc6b41f %}

Usamos a função `macroexpand` para exibir qual código será *evaluated*. Mas se tentarmos executar este código, além da string `If invertido`, a famosa `NullPointerException` será lançada. Mas por que isso acontece?

Neste caso, o Clojure está tentando fazer *evaluate* do resultado da chamada a função `println`, que é `nil`. Olhando a expressão com mais cuidado `(do ((println "If invertido"))))`, dá para perceber que existe um parantêses a mais na história, de maneira que o que é passado para a macro `do` é exatamente o valor `nil`: `(do (nil))`. 

Isso acontece porque quando fizemos o *unquote* do argumento `branches`, no exemplo `(println "If invertido")`, a lista foi *evaluated* e colocada no retorno da *macro*, como num processo similar a variáveis em um template, uma substituição é feita com o valor exato que foi *evaluated*.

O *Unquote Splicing* foi criado exatamente para resolver este tipo de situação. A diferença principal é que diferentemente do *unquote* ele permite que múltiplos *forms* sejam inseridos no lugar de um único *unquote splicing*:

{% gist 29affef8d34d276195da %}

Devemos usá-lo para resolver o problema da macro `unless`:

{% gist 9352871097a6f04727d5 %}

Ao invocá-la não receberemos mais a exceção `NullPointerException`:

{% gist eda92675e46021007567 %}
