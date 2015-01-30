---
layout: post
title: "Deixe seu código mais legível usando Thread Macros"
date: 2015-01-30 13:45:06 -0200
comments: true
categories: clojure fp
---

Clojure fornece várias maneiras de criar um `pipeline` de funções, dentro de um `let`, por exemplo, você pode fazer várias transformações sobre uma coleção. O problema é que muitas vezes esse `pipeline` pode causar confusão durante a leitura do código, especialmente para aqueles que não possuem experiência com linguagens funcionais. É possível deixá-lo mais legível usando as *thread macros*. <!-- more --> 

Considere o exemplo abaixo que cria alguns livros (representando-os como `Map`):

{% gist 35434a76eee29aff322d %}

Com essa coleção de livros em mãos, agora é necessário somar o valor apenas dos livros referentes a linguagem Ruby:

{% gist cf186a1b54ea29cfd4a6 %}

Um desenvolvedor familiarizado com programação funcional, mais especificamente com Lisp, não terá dificuldades para entender o que se passa no código acima:

1. A partir de uma sequência de livros
2. Filtre todos cuja linguagem é Ruby
3. Obtenha o preço de cada um deles
4. Some o valor de todos eles

Lembre-se que códigos Lisp são lidos mais facilmente de dentro para fora, um pouco diferente do que estamos acostumados a ver em outras linguagens.

Clojure possui duas macros que ajudam na criação de códigos mais legíveis, na prática facilitam a leitura do `pipeline`, de um modo que podemos lê-lo de maneira sequencial. O código acima pode ser melhorado com o uso da macro *thread last* `->>`:

{% gist d775bd54692f0a5d7093 %}

A macro *thread last* `->>` pega o elemento passado no primeiro `form` e passa-o como último argumento do `form` seguinte. No código acima, isso significa passar `seq books` como último argumento para `filter #(= (:language %) "Ruby")`. O resultado do `filter` então é passado como último argumento do `map :price`, e assim por diante.

Esse comportamente pode ser visto utilizando a macro `macroexpand-all`, que é capaz de retornar o resultado da chamada a macro *thread last*:

{% gist a402ebb5296e1c08e6e4 %}

O código é quase idêntico a versão sem *thread last*, exceto pela função anônima que declaramos usando um `alias`.

Existe outra macro que funciona de maneira bem semelhante e nos ajuda e muito a criar `pipelines` mais legíveis.

Vamos supor que em algum momento seja necessário adicionar mais informações dentro do `Map` que representa cada um dos livros, por exemplo, informações sobre o autor:

{% gist a76a781d5ab29a345023 %}

Se quisermos apenas o email do autor, normalmente faríamos `(:email (:author ruby-a-linguagem-mais-divertida))`. Com muitos `Map` aninhados o código pode se tornar novamente não muito legível.

A macro *thread first* `->`, que passa um determinado `form` como segundo argumento ao invés do primeiro ao próximo `form`, pode deixar nosso último `pipeline` mais agradável. 

Executar `(-> ruby-a-linguagem-mais-divertida :author :email)` é o mesmo que passar o *form* `ruby-a-linguagem-mais-divertida` como segundo argumento do *form* `:author`, ou seja, `(:author ruby-a-linguagem-mais-divertida)`. Por fim, passar o resultado do último form como segundo argumento do *form* `:email`, ou seja, `(:email {:email "lucasas@gmail.com", :name "Lucas Souza"})`


Usando a macro `macroexpand-all` é possível checar que o que ocorre é exatamente o que foi descrito acima:

{% gist cbf45ffe7a5b104f26a1 %}

Em resumo, comprovamos que é possível deixar códigos com `pipeline` mais legíveis usando *thread first* e *thread last*.

No próximo post vou mostrar que a combinação entre ambas pode ser mais útil ainda.
