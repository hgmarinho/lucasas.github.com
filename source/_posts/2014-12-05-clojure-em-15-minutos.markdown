---
layout: post
title: "Clojure em 15 minutos"
date: 2014-12-05 16:04:28 -0200
comments: true
categories: clojure fp
---

Decidi que começaria a estudar alguma linguagem funcional este ano. O "problema" era escolher 
uma linguagem que tivesse mercado (afinal, já que quero aprender algo novo, vou unir o útil ao agradável e
aprender algo que me traga retorno), que tivesse uma comunidade ativa e que não tivesse uma syntax extramamente
bizarra. <!--more-->

<figure>
  <img src="{{ root_url }}/images/posts/clojure-logo.png"/>
</figure>

Analisando todos estes fatores, decidi que Clojure seria a linguagem mais apropriada para o meu momento. Clojure 
é bastante adotada em empresas fora do Brasil e algumas empresas por aqui estão utilizando também. A [comunidade no Brasil](http://www.meetup.com/clj-sp) está crescendo e existem [ótimos desenvolvedores](https://twitter.com/p_balduino) apostando nela.

Não vou entrar em detalhes sobre instalação e características da linguagem. Você pode ler mais sobre isso na [documentação oficial da linguagem](http://clojure.org), que alias, é excelente.

Clojure é um [dialeto Lisp](http://pt.wikipedia.org/wiki/Lisp), o que na minha opinião torna a linguagem muito simples de aprender, porém, muitos desenvolvedores acham que pode ser um processo lento seu aprendizado.

Nesse primeiro post, [baseado no post "Clojure in 15 minutes" de Adam Bard](http://adambard.com/blog/clojure-in-15-minutes/), de uma série de vários outros que pretendo escrever, vou abordar o básico da linguagem.

```
; Comentários podem ser escritos com ponto e vírgula

; Clojure é escrito usando "forms", que são listas 
; de argmentos dentro de () separados por espaço em branco.
;
; O primeiro argumento sempre é uma função ou macro e 
; o restante são argumentos que serão passados para esta função
;
; Por exemplo:
(+ 1 2) ; => 3
; 
; + é função, os valores 1 e 2 são passados para função 
;
; Mais alguns exemplos:
(+ 1 1) ; => 2
(- 2 1) ; => 1
(* 1 2) ; => 2
(/ 2 1) ; => 2

; str criará uma nova string contendo todos argumentos passados
(str "Hello" " " "World") ; => "Hello World"

; Testes de igualdade são sempre feitos com `=`:
(= 1 1) ; => true
(= 2 1) ; => false

; Quando você deseja inverter o valor booleano use `not`:
(not true) ; => false

; Listas podem ser encadeadas. Clojure as executará de dentro para fora: 
(+ 1 (- 3 2)) ; = 1 + (3 - 2) => 2

; Tipos
;;;;;;;;;;;;;

; Clojure utiliza alguns tipos definidos no Java. Exemplo: booleanos, strings e números.
; Use `class` para verificar o tipo de um determinado valor
(class 1) ; Inteiros literais são java.lang.Long por padrão
(class 1.); Float literais são java.lang.Double
(class ""); Strings sempre são envoltas de aspas duplas, e são java.lang.String
(class false) ; Booleanos são java.lang.Boolean
(class nil); O valor "null" é nil

; Collections e Sequences
;;;;;;;;;;;;;;;;;;;

; Vetores
(class [1 2 3]) ; => clojure.lang.PersistentVector

; Listas
; Para não serem interpretadas como listas que devem ser lidas
; como as anteriores, adicionamos `'`
(class '(1 2 3)) ; => clojure.lang.PersistentList

; Usar a função `list` também é uma opção
(class (list 1 2 3)) ; => clojure.lang.PersistentList

; Ambas estruturas são collections
(coll? '(1 2 3)) ; => true
(coll? [1 2 3]) ; => true

; Porém, apenas listas são sequencias
(seq? '(1 2 3)) ; => true
(seq? [1 2 3]) ; => false

; Use `cons` para adicionar um item no começo de uma lista ou vetor
(cons 4 [1 2 3]) ; => (4 1 2 3)
(cons 4 '(1 2 3)) ; => (4 1 2 3)

; Use `conj` para adicionar um item no começo de uma lista
; ou no fim de um vetor
(conj [1 2 3] 4) ; => [1 2 3 4]
(conj '(1 2 3) 4) ; => (4 1 2 3)

; Use `concat` para juntar duas collections
(concat [1 2] '(3 4)) ; => (1 2 3 4)

; Use `map` para percorrer uma collection retornando outra
; com o resultado da operação feita. Por exemplo: A função `inc`
; utilizada com `map`, incrementará cada item da collection retornando
; uma nova
(map inc [1 2 3]) ; => (2 3 4)

; Use `filter` para percorrer e filtrar uma collection. Por exemplo:
(filter even? [1 2 3]) ; => (2)

; Use `reduce` para executar e agrupar os resultados
(reduce + [1 2 3 4])
; = (+ (+ (+ 1 2) 3) 4)
; => 10

; `reduce` pode receber uma argumento com valor inicial
(reduce + 1 [3 2 1])
; = (+ (+ (+ 1 3) 2) 1)
; => 7

; Para criar uma lista literal de dados, use '
'(+ 1 2) ; => (+ 1 2)

; Para executá-la use `eval`
(eval '(+ 1 2)) ; => 3


; Funções
;;;;;;;;;;;;;;;;;;;;;

; Use `fn` para criar funções anônimas. 
; Uma função sempre retorna seu último statement.
(fn [] "Olá") ; => fn

; Para invocá-la basta declará-la dentro de `()`
((fn [] "Olá")) ; => "Olá"

; Para guardar uma função em uma variável basta usar `def`
(def ola (fn [] "Olá"))
(ola) ; => "Olá"

; O processo pode ser encurtado usando `defn`
(defn ola [] "Olá")
(ola) ; => "Olá"

; `[]` representa o conjunto de parâmetros recebidos pela função
(defn ola [nome]
  (str "Olá " nome))
(ola "Lucas") ; => "Olá Lucas"

; É possível sobrecarregar e criar várias assinaturas uma função
(defn ola
  ([] "Olá desconhecido")
  ([nome] (str "Olá " nome)))
(ola "Lucas") ; => "Olá Lucas"
(ola) ; => "Olá desconhecido"

; Funções podem receber argumentos extras e disponibilizá-los
; em uma seq
(defn funcao-com-muitos-argumentos [& args]
  (str "Você passou " (count args) " argumentos: " args))
(funcao-com-muitos-argumentos 1 2 3) ; => "Você passou 3 argumentos: (1 2 3)"

; É possível receber argumentos pré-definidos e extras
(defn funcao-com-muitos-argumentos [nome & args]
  (str "Oláá " nome ", você passou " (count args) " argumentos extra"))
(funcao-com-muitos-argumentos "Lucas" 1 2 3)
; => "Olá Lucas, você passou 3 argumentos extra"


; Hashmaps
;;;;;;;;;;

(class {:a 1 :b 2 :c 3}) ; => clojure.lang.PersistentArrayMap

; Keywords são como string
(class :a) ; => clojure.lang.Keyword

; Maps podem user qualquer tipo como chame, mas o mais comum é que sejam keywords
(def stringmap (hash-map "a" 1, "b" 2, "c" 3))
stringmap  ; => {"a" 1, "b" 2, "c" 3}

(def keymap (hash-map :a 1 :b 2 :c 3))
(keymap) ; => {:a 1, :c 3, :b 2} (Como todo HashMap, a ordem não é garantida)

; A propósito, vírgulas são ignoradas e tratadas como espaços em branco
; por isso, você pode definir um map separados os conjuntos de chave e valores
; apenas por espaço.

; Keywords podem ser utilizadas diretamente para pegar o valor de uma HashMap 
(:b keymap) ; => 2

; Não tente com String, isso não é Rails ;-)
;("a" stringmap)
; => Exception: java.lang.String cannot be cast to clojure.lang.IFn

; Keywords que não existem no HashMap retornam sempre nil
(:d keymap) ; => nil

; Use `assoc` para adicionar novas chaves
(assoc keymap :d 4) ; => {:a 1, :b 2, :c 3, :d 4}

; Mas lembre-se que Clojure trata tudo como imutável. Sendo assim:
keymap ; => {:a 1, :b 2, :c 3}

; Use `dissoc` para remover chaves
(dissoc keymap :a :b) ; => {:c 3}

; Sets
;;;;;;

(class #{1 2 3}) ; => clojure.lang.PersistentHashSet
(set [1 2 3 1 2 3 3 2 1 3 2 1]) ; => #{1 2 3}

; Adicione um item usando `conj`
(conj #{1 2 3} 4) ; => #{1 2 3 4}

; Remova um item usando `disj`
(disj #{1 2 3} 1) ; => #{2 3}

; Teste se um item existe em um set utilizando-o como uma função
(#{1 2 3} 1) ; => 1
(#{1 2 3} 4) ; => nil

; Outros forms
;;;;;;;;;;;;;;;;;

; Estruturas lógicas são macros no Clojure, e sua syntax
; é igualmente simples a todo resto.
(if false "a" "b") ; => "b"
(if false "a") ; => nil

; Use `let` para criar escopos temporários
; Neste exemplo, as variáveis `a` e `b` valem, respectivamente, `1` e `2`
(let [a 1 b 2]
  (+ a b)) ; => 3

E não podem ser utilizadas fora do `let`
(let [a 1 b 2]
  (+ a b)) ; => 3

(+ a b) ; => Unable to resolve symbol: a in this context 

; Grupos de statements podem ser feitos utilizando `do`:
(do
  (print "Olá")
  "Mundo") ; => "Olá" (prints "Mundo")

; Funções tem um `do` implícito
(defn imprime-e-diz-ola [nome]
  (print "Falando olá para " name)
  (str "Olá " name))
(imprime-e-diz-ola "Lucas") ;=> "Olá Lucas" (prints "Falando olá para Lucas")

; `let` também possui `do` implícito
(let [nome "Lucas"]
  (print "Falando olá para " name)
  (str "Olá " name)) ;=> "Olá Lucas" (prints "Falando olá para Lucas")
```

Isso é o básico para você conseguir brincar com Clojure.
