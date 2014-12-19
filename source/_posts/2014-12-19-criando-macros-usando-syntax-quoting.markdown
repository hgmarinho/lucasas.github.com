---
layout: post
title: "Criando macros com Syntax Quoting"
date: 2014-12-19 19:43:40 -0200
comments: true
categories: fp clojure macros
---

No último post falei um pouco sobre [macros](http://lucasas.github.io/blog/2014/12/10/pattern-matching-clojure-erlang). Como *macros* é um assunto um pouco extenso, vou escrever mais alguns posts sobre detalhes avançados que podemos utilizar no dia-a-dia. <!-- more -->

Só relembrando: *Macros* permitem estender a própria linguagem, ou seja, escrever a própria linguagem do jeito que você quiser. Podemos pegar como exemplo a macro `if-not`:

{% gist 5502e5dfa9c79167b882 %}

Essa *macro* é built-in na própria linguagem, mas caso não existesse, ao invés de chamar a função `if` invertendo o valor booleano `(if (not (zero? 0)) :then :else)`, poderíamos criar a mesma macro e deixar a chamada de código mais concisa `(if-not (zero? 0) :then :else)`.

A *macro* `when` é outro bom exemplo de como uma *macro* pode ajudar o código a ficar mais conciso:

{% gist b95446596a42b66204ca %}

Caso a condição passada seja `true`, um bloco com múltiplas chamadas é executado, similar ao [`do`](https://clojuredocs.org/clojure.core/do). Na real, por baixo dos panos, a chamada na *macro* `when`, retorna uma lista que será `evaluated`. Mas que lista é esta? Vamos verificar usando a função [macroexpand](https://clojuredocs.org/clojure.core/macroexpand):

{%gist a59b0bf59985df776119 %}

Como eu falei, nada mais que uma lista com chamadas para `if` e `do`. Isso acontece porque precisamos que uma lista seja retornada. Para que o Clojure possa dar `evaluate` nela, como se fora uma chamada para uma função qualquer, por exemplo: `(+ 1 2) ; => 2`.

Dentro de uma *macro* podemos usar qualquer outro código Clojure, chamar funções, por exemplo. Mas por que o `if` e o `do` não são *evaluated*? Porque usamos *Simple Quoting* para informar a linguagem que aquele pedaço de código não deve ser *evaluated*. 

Vamos criar uma *macro* chamada `unless` (ok, já sei, existe o `if-not`) que recebe uma condicional e executa o bloco passado, que pode ser mais de uma lista, caso a condição seja `false`:

{% gist 18b0b15d832815c982f3 %}

Tivemos que fazer o *quote* do `if` para dizer que ele é um símbolo `unevaluated` na lista retornada. Porém, o código não ficou tão logível quanto deveria ficar, dependendo do tamanha da *macro* o código pode se tornar muito verboso e quase inelegível.

Para estes casos, podemos utilizar *Syntax Quoting*, um tipo de *quote* mais poderoso:

{% gist 64b1e2f16a264a8bf03b %}

*Syntax Quoting* permite que uma lista *unevaluated* seja retornada, dando o mesmo efeito do *Simple Quoting*, mas com um código bem mais sucinto. 

Uma das diferenças é que os símbolos são retornados com seu nome completo, por exemplo:

{% gist fd2f428264e5f9b76c35 %}

Olhando o retorno da macro `unless` percebemos algo errado. Os parâmetros `test` e `branches` foram *quoted* também, quando na verdade deveriam ser `(> 2 1)` e `(println "If invertido")`. Se tentarmos invocar a *macro* acima, uma exceção informando que os símbolos não existem.

Podemos informar ao Clojure que faça *unquote* utilizando *til* (~):

{% gist afddd9cca1f57dc6b41f %}

Quando um *til* aparece dentro de um código *syntax quoted*, o Clojure faz um *evaluate* destes *forms*. No próximo post falremos sobre *Unquote Splicing*. 
