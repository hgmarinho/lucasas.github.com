---
layout: post
title: "Pattern Matching, Clojure e Elixir"
date: 2014-12-10 23:38:41 -0200
comments: true
categories: clojure fp erlang elixir
---

No último sábado, tivemos uma aprensentação do [@arthurgeek](https://twitter.com/arthurgeek) e do [@ricardo](https://twitter.com/almeidaricardo) sobre Elixir aqui no [GetNinjas](http://www.getninjas.com.br). Dentre as várias coisas interessantes da linguagem, eles mostraram *pattern matching* em funções. <!--more-->

Clojure, assim como a maioria das linguagens funcionais, implementa *pattern matching*. Mas primeiro vamos ver um código que não utiliza *pattern matching*, o conhecido e famigerado *FizzBuzz*:

{% gist 1c5aacfbef839ef6fae7 %}

Primeiro criamos uma coleção chamada `numeros` com um range de 1 a 100. A função `doseq` itera a coleção de números, o restante do código é a implementação básica do *FizzBuzz*:

1. Calculamos o resto da divisão com 3 e 5, bindamos o resultado nos símbolos `mod_3` e `mod_5`, respectivamente.
2. A função `cond` é similar ao `switch` de outras linguagens, aceitando múltiplas condições. O suficiente para implementarmos as quatro condições do *FizzBuzz*.

Vamos substituir as múltiplas condições da função `cond`, por um *pattern matching*, cuja uma das *features* é fazer asserção no conteúdo de uma variável:

{% gist ba623bf9ece9ae83388f %}

Na primeira linha o objetivo é usar a função `use` para tornar disponível a função `match` pertencente ao módulo `clojure.core.match`, algo como importar a função para dentro do escopo onde iteramos a coleção.

A função `match` define dentro dos `[]` os valores que podemos fazer asserção. A primeira asserção feita verifica se os dois valores são `0`, caso positivo `FizzBuzz` é retornado para a função `println`, caso contrário, a próxima asserção é feita, neste caso, se o primeiro valor `(mod n 3)` for `0`, o valor `Fizz` é retornado. Se essa asserção falhar, a terceira é feita verificando que `(mod n 5)` é `0`, retornando `Buzz`. Caso nenhuma asserção for verdadeira, a opção `else` é executada e o valor `n` é retornado.  

Na segunda asserção utilizamos `_` para que o Clojure ignore o segundo valor, ou seja, não importa o que `(mod n 5)` retorne, se o primeiro valor `(mod n 3)` for `0`, "Fizz" será retornado. O mesmo vale para a terceira asserção, obviamente, muda-se apenas o valor ao qual fazemos a verificação.

*Pattern Matching* possui várias características legais que vou comentar ao longo dos outros posts. Mas uma *feature* do Elixir em particular que eu gostei muito, não existe (eu pelo menos não achei :P) no Clojure, que a capacidade fazer *match* na declaração de funções. Por exemplo o seguinte código em Elixir:

{% gist 59a7db92fef8c6051d44 %}

O *pattern matching* do Elixir consegue executar determinada função baseado no valor passado como argumento, ou seja, caso `Factorial.of(2)` seja invocada, o compilador sabe que a função `n * of(n - 1)` deve ser executada, quando `Factorial.of(0)` for chamado, o compilador executa a função que retorna `1` e quando `Factorial.of(1)` for chamado, a outra função que retorna `1` será executada. A legibilidade do código aumenta, conseguimos ler: *fatorial de 0 = 1* e *fatorial de n = n * fatorial(n - 1)*.

Como sabemos chamadas recursivas precisam de uma condição de parada. Normalmente no cálculo de fatorial a condição é `(= n 0) ; => true`, porém, aprendemos com o exemplo anterior que é possível remover `if` usando *pattern matching*. Uma versão do cálculo fatorial usando `match` ficaria assim:

{% gist 9f1007e93186c8011840 %}

Mesmo assim, o código, na minha opinião, não fica tão legível quanto fica no exemplo escrito em Elixir.

A boa notícia é que conseguimos alcançar o mesmo comportamento em Clojure usando *macros*.

Como todos os dialetos Lisp, Clojure é uma linguagem [*homoiconic*](https://en.wikipedia.org/wiki/Homoiconic) que nos dá o poder de criar rotinas que escrevem código. Só tenha cuidado antes de criar uma *macro* porque é considerada uma má prática criá-las quando podemos resolver os problemas utilizando funções comuns.

Criar uma *macro* é simples:

{% gist e2e7706dc7e154b0153e %}

No exemplo do fatorial, onde queremos usar *pattern matching* na definição das funções, podemos utilizar a macro [defun](https://github.com/killme2008/defun), que por baixo dos panos cria uma função que utiliza o mesmo `match` que aprendemos a utilizar. Como havia dito, o código fica um pouco mais legível:

{% gist 1127210fb9b53325821e %}

*Macros* permitem que o compilador seja extendido por códigos escritos pelo usuário. Isso nos permite criar códigos que são mais concisos, legíveis e significativos. [Leia mais](http://clojure.org/macros) sobre esse poderoso recurso da linguagem.
