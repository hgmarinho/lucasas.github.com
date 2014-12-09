---
layout: post
title: "Clojure e recursão"
date: 2014-12-08 17:16:46 -0200
comments: true
categories: clojure fp
---

Vamos ver um pouco sobre recursão e como resolver problemas de *tail recursion* utilizando Clojure. <!--more-->

<figure>
  <img src="{{ root_url }}/images/posts/recursion.jpg"/>
</figure>

Você provavelmente já deve ter se deperado com algum código Javascript como o abaixo:

```
var numbers = [1, 5, 6, 8, 10, 3, 45];
var total = 0;

for(var i=0; i < numbers.length; i++) {
  total += numbers[i];
}
```

Tanto a variável `i` quanto a variável `total` são mutáveis, o que sabemos ser perigoso quando utilizamos paralelismo. Clojure não permite valores mutáveis, portanto, este tipo de problema não nos afeta. Em Clojure resolvemos estes problemas utilizando recursão, o jeito funcional de iterar uma coleção e construir um resultado.

Vamos criar uma função chamada `soma`, que obviamente, soma todos os valores de uma coleção. O código a seguir usa recursão para resolver o problema:

```
(defn soma
  ([valores acumulador]
     (if (empty? valores)
       acumulador
       (soma (rest valores) (+ (first valores) acumulador)))))
```

A função recebe dois argumentos, a coleção que será iterada e um acumulador da soma de cada elemento. Como toda função recursiva precisamos de uma condição de parada, no exemplo essa condição verifica se a coleção `valores` é `empty?`. Se a condição for verdadeira, sabemos que todos os elementos da coleção foram somados, por isso, retornamos o valor `acumulador`.

Mas se a condição não for verdadeira, isso significa que precisamos processar o restante da coleção (`tail`), então recursivamente chamamos a mesma funcao `soma` apenas com o `tail`, o `acumulador` passado é a soma do primeiro (`first`) elemento da coleção. Executamos a função recursivamente até que `valores` seja `empty?`.

Cada chamada da função `soma` criará um novo escopo, onde `valores` e `acumulador` são bindados em outros valores, tudo sem mutações.

A chamada para a função `soma` fará o trabalho esperado:

```
(soma [1 2 5] 0) ; => 8
```

A função `soma` chamada com poucos elementos não apresenta problemas de performance:

```
(time (soma [1 2 5] 0)) ; => "Elapsed time: 0.047669 msecs"
```

Porém, se chamarmos com um número grande de elementos:

```
(soma (range 10000) 0) ; => StackOverflowError   clojure.lang.ChunkedCons.more
```

O código acima causa `10001` chamadas a função `soma`. No momento da última chamada, a primeira chamada está esperando pela segunda, a segunda está esperando pela terceira, a terceira está esperando pela quarta, e assim sucessivamente. Por fim, a milionésima primeira chamada está esperando pela milionésima. Cada uma destas chamadas estarão consumindo memória. 

Mas o que a milionésima primeira chamada fará com o resultado da milionésima? Absolutamente nada. A chamada recursiva é a última coisa feita, por isso o resultado é apenas passado de volta para o *caller*, que passa o resultado para outro *caller* até que chegue no *caller* original. Esse *pattern* é conhecido como *tail-recursion*.


Alguns compiladores são espertos o suficiente para perceber que existe uma *tail recursion* e reescrevem a função utilizando um `loop` internamente. Mas, por limitações da JVM, Clojure não faz isso automaticamente. É necessário dizer ao compilador que você tem uma *tail recursion*. Em Clojure basta usar a função `recur`:

```
(defn soma
  ([valores acumulador]
     (if (empty? valores)
       acumulador
       (recur (rest valores) (+ (first valores) acumulador)))))
        ; substituímos a função `soma` por `recur`
```

Com apenas uma mudanças ganhamos uma grande melhoria de performance:

```
(soma (range 1000) 0) ; => "Elapsed time: 0.219801 msecs"
```

Rodando a função `soma` antiga (com mil elementos na coleção), o tempo foi em média 4 vezes maior que a função que utiliza `recur`.
