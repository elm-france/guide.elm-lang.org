# Lire les types

Au chapitre [Bases du langage](/bases_du_langage.html), nous avons pu manipuler un certain nombre d'exemples interactifs pour prendre le langage en main. Poursuivons cette démarche en nous interrogeant cette fois sur le **type** des valeurs manipulées.


## Types primitifs et listes

Saisissons quelques expressions simples et observons ce qui en résulte :

{% replWithTypes %}
[
	{
		"input": "\"hello\"",
		"value": "\u001b[93m\"hello\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "not True",
		"value": "\u001b[96mFalse\u001b[0m",
		"type_": "Bool"
	},
	{
		"input": "round 3.1415",
		"value": "\u001b[95m3\u001b[0m",
		"type_": "Int"
	}
]
{% endreplWithTypes %}

Cliquez sur cette boîte noire juste au-dessus ⬆️ ; le curseur devrait commencer à clignoter. Saisissez `3.1415` et appuyez sur la touche Entrée de votre clavier. Cela devrait afficher `3.1415` suivi du type `Float`.

Que se passe t-il concrètement ici ? Chaque entrée affiche une valeur suivie de son **type**. Vous pouvez lire ces lignes à haute voix :

- La valeur `"hello"` est une `String` (une chaîne de caractère).
- La valeur `False` est un `Bool` (un booléen).
- La valeur `3` est un `Int` (un nombre entier).
- La valeur `3.1415` est un `Float` (un nombre à virgule flottante).

Elm est capable de deviner le type de n'importe quelle valeur que vous lui envoyez ! Regardons ce que ça donne avec les listes :

{% replWithTypes %}
[
	{
		"input": "[ \"Alice\", \"Bob\" ]",
		"value": "[\u001b[93m\"Alice\"\u001b[0m,\u001b[93m\"Bob\"\u001b[0m]",
		"type_": "List String"
	},
	{
		"input": "[ 1.0, 8.6, 42.1 ]",
		"value": "[\u001b[95m1.0\u001b[0m,\u001b[95m8.6\u001b[0m,\u001b[95m42.1\u001b[0m]",
		"type_": "List Float"
	}
]
{% endreplWithTypes %}

Vous pouvez lire ces types ainsi :

1. Une `List` contenant des valeurs de type `String`.
2. Une `List` contenant des valeurs de type `Float`.

Au final, un **type** est la description sommaire du contenu d'une valeur.


## Fonctions

Regardons le type de quelques fonctions :

{% replWithTypes %}
[
	{
		"input": "String.length",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> Int"
	}
]
{% endreplWithTypes %}

Essayez d'entrer `round` or `sqrt` pour observer d'autres types ⬆️

La fonction `String.length` est de type `String -> Int`. Cela signifie qu'elle *doit* prendre un argument de type `String` et qu'elle retourne une valeur de type `Int`. Essayons de lui fournir un argument :

{% replWithTypes %}
[
	{
		"input": "String.length \"Supercalifragilisticexpialidocious\"",
		"value": "\u001b[95m34\u001b[0m",
		"type_": "Int"
	}
]
{% endreplWithTypes %}

Donc on prend une fonction `String -> Int`, on lui passe un argument `String`, et on obtient un `Int` en retour.

Mais que se passe t-il quand on donne autre chose qu'une valeur de type `String` ? Essayez d'entrer `String.length [1,2,3]` ou `String.length True` pour voir ce que ça donne ⬆️

Vous allez découvrir qu'une fonction `String -> Int` doit *absolument* recevoir un argument de type `String` !

> **Note:** Plus Les fonctions prennent d'arguments, plus elles contiennent de *flèches* (`->`). Par exemple, cette fonction prend deux arguments :
>
> {% replWithTypes %}
[
	{
		"input": "String.repeat",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "Int -> String -> String"
	}
]
{% endreplWithTypes %}
>
> Fournir deux arguments à `String.repeat` comme `String.repeat 3 "ha"` produira `"hahaha"`. On peut retenir que `->` est une façon un peu étrange de séparer les arguments, mais nous expliquons le raisonnement sous-jacent [ici](/appendix/function_types.md).


## Annotations de type

Jusqu'ici nous avons laissé Elm deviner les types, mais on peut également fournir une **annotation de type** au-dessus de la ligne définissant une fonction, comme ceci :

```elm
half : Float -> Float
half n =
  n / 2

-- half 256 == 128
-- half "3" -- error!

hypotenuse : Float -> Float -> Float
hypotenuse a b =
  sqrt (a^2 + b^2)

-- hypotenuse 3 4  == 5
-- hypotenuse 5 12 == 13

checkPower : Int -> String
checkPower powerLevel =
  if powerLevel > 9000 then "It's over 9000!!!" else "Meh"

-- checkPower 9001 == "It's over 9000!!!"
-- checkPower True -- error!
```

Ajouter des annotations de type n'est pas obligatoire, mais c'est fortement recommandé ! Parmi leurs nombreux bénéfices :

1. **Qualité des messages d'erreur** &mdash; Quand vous ajoutez une annotation de type, le compilateur comprend ce que vous _essayez_ de faire. Votre implémentation peut comporter des erreurs, mais le compilateur peut maintenant les comparer à votre intention initiale. &ldquo;Vous avez dit que `powerLevel` était un `Int`, mais il est utilisé comme une `String` !&rdquo;
2. **Documentation** &mdash; Quand vous revenez sur une base de code ancienne (ou quand d'autres collègues la découvrent pour la première fois), il est très pratique de lire directement ce qui rentre et sort d'une fonction, sans avoir à lire l'implémentation très attentivement.

Toutefois, il est possible de se tromper en écrivant des annotations. Dans ce cas, que se passe t-il si une annotation ne correspond pas à son implémentation ? Le compilateur infère tous les types et vérifie systématiquement que votre annotation correspond à la réalité. En d'autres termes, le compilateur assure la cohérence des annotations en permanence. Par conséquent, vous disposez des meilleurs messages d'erreur possibles _et_ d'une documentation toujours à jour !


## Variables de type

En lisant du code Elm, vous pouvez rencontrer des annotations comportant une ou plusieurs lettres en minuscule, comme par exemple celle de la fonction `List.length` :

{% replWithTypes %}
[
	{
		"input": "List.length",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "List a -> Int"
	}
]
{% endreplWithTypes %}

La lettre `a` dans le type `List a -> Int` est une **variable de type**, qui peut varier en fonction de l'usage fait de [`List.length`][length] :

{% replWithTypes %}
[
	{
		"input": "List.length [1,1,2,3,5,8]",
		"value": "\u001b[95m6\u001b[0m",
		"type_": "Int"
	},
	{
		"input": "List.length [ \"a\", \"b\", \"c\" ]",
		"value": "\u001b[95m3\u001b[0m",
		"type_": "Int"
	},
	{
		"input": "List.length [ True, False ]",
		"value": "\u001b[95m2\u001b[0m",
		"type_": "Int"
	}
]
{% endreplWithTypes %}

Nous ne nous intéressons qu'à la longueur de ces listes, sans jamais nous soucier du type de données qu'elles contiennent. La variable de type `a` indique qu'on peut cibler n'importe quel type. Regardons un autre exemple courant :

{% replWithTypes %}
[
	{
		"input": "List.reverse",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "List a -> List a"
	},
	{
		"input": "List.reverse [ \"a\", \"b\", \"c\" ]",
		"value": "[\u001b[93m\"c\"\u001b[0m,\u001b[93m\"b\"\u001b[0m,\u001b[93m\"a\"\u001b[0m]",
		"type_": "List String"
	},
	{
		"input": "List.reverse [ True, False ]",
		"value": "[\u001b[96mFalse\u001b[0m,\u001b[96mTrue\u001b[0m]",
		"type_": "List Bool"
	}
]
{% endreplWithTypes %}

À nouveau, la variable de type `a` peut changer en fonction de l'usage de [`List.reverse`][reverse]. Mais ici, nous avons un `a` dans l'argument *et* le résultat. Cela signifie que quand vous passez une `List Int`, vous récupérez une `List Int` en retour également. Une fois décidé à quoi correspond la variable de type `a`, le type sous-jacent doit être cohérent partout.

> **Note :** Les variables de type doivent commencer par un caractère minuscule, mais elles peuvent tout aussi bien être des mots entiers. Nous pourrions écrire le type de `List.length` avec une signature `List value -> Int` et celui de `List.reverse` avec `List element -> List element`. Aucun problème tant que le nom de la variable commence par une lettre minuscule. Les variables de type `a` et `b` sont couramment utilisées par convention, mais certaines annotations bénéficient des noms plus appropriés.

[length]: https://package.elm-lang.org/packages/elm/core/latest/List#length
[reverse]: https://package.elm-lang.org/packages/elm/core/latest/List#reverse


## Variables de type contraintes

Il y a une variante spéciale des variables de type en Elm appelée *variable de type **contrainte***. L'une des plus courantes est `number`, qu'utilisent de nombreuses fonctions comme [`negate`](https://package.elm-lang.org/packages/elm/core/latest/Basics#negate) par exemple :

{% replWithTypes %}
[
	{
		"input": "negate",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "number -> number"
	}
]
{% endreplWithTypes %}

Essayez de soumettre des expressions comme `negate 3.1415` ou `negate (round 3.1415)`, puis `negate "coucou"` ⬆️

Normalement, les variables de type peuvent être associées à n'importe quel type, mais `number` ne peut l'être qu'avec `Int` ou `Float`. Ici la variable `number` _contraint_ les possibilités.

La liste complète des variables de type contraintes comprend :

- `number`, qui autorise `Int` et `Float`
- `appendable`, qui autorise `String` et `List a`
- `comparable`, qui autorise `Int`, `Float`, `Char`, `String`, et les listes/tuples de `comparable`
- `compappend`, qui autorise `String` et `List comparable`

Ces variables de type contraintes existent pour rendre certains opérateurs comme `(+)` et `(<)` un peu plus flexibles.

Maintenant que nous avons vu les types pour les valeurs et les fonctions plutôt exhaustivement, voyons comment construire des structures de données plus complexes.
