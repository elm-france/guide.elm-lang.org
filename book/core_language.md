# Bases du langage

L'objectif est de vous familiariser avec les **valeurs** et les **fonctions** afin de faciliter votre lecture du code Elm quand nous aborderons des exemples plus avancés par la suite.


## Valeurs

Le plus petit bloc de construction dans Elm est appelé une **valeur**, comme `42`, `True`, et `"Hello !"` par exemple.

Commençons par regarder les nombres :

{% repl %}
[
	{
		"input": "1 + 1",
		"value": "\u001b[95m2\u001b[0m",
		"type_": "number"
	}
]
{% endrepl %}

Tous les exemples de cette page sont interactifs. Cliquez sur la boîte noire au dessus ⬆️ et le curseur va commencer à clignoter. Tapez `2 + 2` et appuyez sur la touche _Entrée_ pour afficher le résultat `4`. Vous pourrez interagir avec tous les exemples de cette page de la même manière.

Essayez de taper autre chose comme `30 * 60 * 1000` ou `2 ^ 4`. Cela fonctionne comme une calculatrice.

Au-delà des calculs mathématiques, il est bien plus courant de travailler avec des **chaînes de caractères** comme ceci :


{% repl %}
[
	{
		"input": "\"hello\"",
		"value": "\u001b[93m\"hello\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "\"butter\" ++ \"fly\"",
		"value": "\u001b[93m\"butterfly\"\u001b[0m",
		"type_": "String"
	}
]
{% endrepl %}


Essayez d'assembler quelques chaînes de caractères avec l'opérateur `(++)` ⬆️

Ces valeurs primitives deviennent plus intéressantes quand on commence à écrire des fonctions pour les transformer.

> **Note:** Pour en apprendre davantage sur les opérateurs comme [`(+)`](https://package.elm-lang.org/packages/elm/core/latest/Basics#+) et [`(/)`](https://package.elm-lang.org/packages/elm/core/latest/Basics#/) et [`(++)`](https://package.elm-lang.org/packages/elm/core/latest/Basics#++), consultez la documentation du module [`Basics`](https://package.elm-lang.org/packages/elm/core/latest/Basics) (en anglais).


## Fonctions

Une **fonction** est une manière de transformer des valeurs. Elle prend une valeur en entrée et en produit une autre en sortie.

Prenons l'exemple d'une fonction `greet` qui construit une salutation à partir d'un nom :

{% repl %}
[
	{
		"add-decl": "greet",
		"input": "greet name =\n  \"Hello \" ++ name ++ \"!\"\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> String"
	},
	{
		"input": "greet \"Alice\"",
		"value": "\u001b[93m\"Hello Alice!\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "greet \"Bob\"",
		"value": "\u001b[93m\"Hello Bob!\"\u001b[0m",
		"type_": "String"
	}
]
{% endrepl %}

Essayer de dire bonjour à quelqu'un d'autre, comme `"Charlie"` ou `"Diane"` ⬆️

Les valeurs passées en entrée de la fonction sont généralement appelées **arguments**. On pourrait alors dire que «`greet` est une fonction qui prend un argument ».

Mettons la politesse de côté pour introduire une nouvelle fonction qui prend _deux_ arguments :

{% repl %}
[
	{
		"add-decl": "madlib",
		"input": "madlib animal adjective =\n  \"The ostentatious \" ++ animal ++ \" wears \" ++ adjective ++ \" shorts.\"\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> String -> String"
	},
	{
		"input": "madlib \"cat\" \"ergonomic\"",
		"value": "\u001b[93m\"The ostentatious cat wears ergonomic shorts.\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "madlib (\"butter\" ++ \"fly\") \"metallic\"",
		"value": "\u001b[93m\"The ostentatious butterfly wears metallic shorts.\"\u001b[0m",
		"type_": "String"
	}
]
{% endrepl %}


Essayez de passer deux arguments à la fonction `madlib` ⬆️

Remarquez l'utilisation des parenthèses pour grouper `"butter" ++ "fly"` ensemble dans le deuxième exemple. Chaque argument d'une fonction doit être une valeur primitive comme `"cat"` ou se trouver entre parenthèses.

> **Note:** Les personnes venant de langages comme Javascript peuvent être étonnées par ces différences de notation :

>     madlib "cat" "ergonomic"                  -- Elm
>     madlib("cat", "ergonomic")                // JavaScript
>
>     madlib ("butter" ++ "fly") "metallic"      -- Elm
>     madlib("butter" + "fly", "metallic")       // JavaScript
>
> Bien que surprenant au début, ce style réduit l'utilisation des parenthèses et des virgules. Il donne une sensation de propreté et de minimalisme au langage une fois habitué(e).


## Expressions If

Lorsque que l'on veut obtenir un comportement conditionnel en Elm, on utilise une expression `if`.

Faisons une nouvelle fonction `greet` qui respecte le président Abraham Lincoln à sa juste valeur :

{% repl %}
[
	{
		"add-decl": "greet",
		"input": "greet name =\n  if name == \"Abraham Lincoln\" then\n    \"Greetings Mr. President!\"\n  else\n    \"Hey!\"\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> String"
	},
	{
		"input": "greet \"Tom\"",
		"value": "\u001b[93m\"Hey!\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "greet \"Abraham Lincoln\"",
		"value": "\u001b[93m\"Greetings Mr. President!\"\u001b[0m",
		"type_": "String"
	}
]
{% endrepl %}

Il y a sûrement d'autres cas à gérer, mais ça devrait suffire pour l'exemple.


## Listes

Les listes sont une des structures de données les plus courantes en Elm. Elles contiennent une séquence de choses similaires, comme les tableaux en JavaScript.

Les listes peuvent contenir plusieurs valeurs. Ces valeurs doivent toutes être du même type. Voici quelques exemples qui utilisent des fonctions du module [`List`](https://package.elm-lang.org/packages/elm/core/latest/List) :

{% repl %}
[
	{
		"add-decl": "names",
		"input": "names =\n  [ \"Alice\", \"Bob\", \"Chuck\" ]\n",
		"value": "[\u001b[93m\"Alice\"\u001b[0m,\u001b[93m\"Bob\"\u001b[0m,\u001b[93m\"Chuck\"\u001b[0m]",
		"type_": "List String"
	},
	{
		"input": "List.isEmpty names",
		"value": "\u001b[96mFalse\u001b[0m",
		"type_": "Bool"
	},
	{
		"input": "List.length names",
		"value": "\u001b[95m3\u001b[0m",
		"type_": "String"
	},
	{
		"input": "List.reverse names",
		"value": "[\u001b[93m\"Chuck\"\u001b[0m,\u001b[93m\"Bob\"\u001b[0m,\u001b[93m\"Alice\"\u001b[0m]",
		"type_": "List String"
	},
	{
		"add-decl": "numbers",
		"input": "numbers =\n  [4,3,2,1]\n",
		"value": "[\u001b[95m4\u001b[0m,\u001b[95m3\u001b[0m,\u001b[95m2\u001b[0m,\u001b[95m1\u001b[0m]",
		"type_": "List number"
	},
	{
		"input": "List.sort numbers",
		"value": "[\u001b[95m1\u001b[0m,\u001b[95m2\u001b[0m,\u001b[95m3\u001b[0m,\u001b[95m4\u001b[0m]",
		"type_": "List number"
	},
	{
		"add-decl": "increment",
		"input": "increment n =\n  n + 1\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "number -> number"
	},
	{
		"input": "List.map increment numbers",
		"value": "[\u001b[95m5\u001b[0m,\u001b[95m4\u001b[0m,\u001b[95m3\u001b[0m,\u001b[95m2\u001b[0m]",
		"type_": "List number"
	}
]
{% endrepl %}

Essayez de construire votre propre liste et d'utiliser des fonctions comme `List.length` ⬆️

Gardez en tête que tous les éléments d'une liste doivent être du même type.


## Tuples

Les tuples sont une autre structure de données très utile. Un tuple peut contenir deux ou trois valeurs, chaque valeur pouvant être de type différent. Il est typiquement utilisé pour retourner plusieurs valeurs à partir d'une fonction. La fonction suivante accepte un nom et retourne un message à l'utilisateur :


{% repl %}
[
	{
		"add-decl": "isGoodName",
		"input": "isGoodName name =\n  if String.length name <= 20 then\n    (True, \"name accepted!\")\n  else\n    (False, \"name was too long; please limit it to 20 characters\")\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> ( Bool, String )"
	},
	{
		"input": "isGoodName \"Tom\"",
		"value": "(\u001b[96mTrue\u001b[0m, \u001b[93m\"name accepted!\"\u001b[0m)",
		"type_": "( Bool, String )"
	}
]
{% endrepl %}

Les tuples sont pratiques pour des cas simples, mais il est souvent préférable d'utiliser des _records_ pour des traitements plus complexes.


## Records

Un _**record**_ (ou enregistrement) peut contenir plusieurs valeurs, chaque valeur étant associée à un nom.

Voici un _record_ qui représente l'économiste britannique John A. Hobson :

{% repl %}
[
	{
		"add-decl": "john",
		"input": "john =\n  { first = \"John\"\n  , last = \"Hobson\"\n  , age = 81\n  }\n",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m81\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	},
	{
		"input": "john.last",
		"value": "\u001b[93m\"Hobson\"\u001b[0m",
		"type_": "String"
	}
]
{% endrepl %}

Nous avons défini un _record_ composé de trois **champs** qui contiennent des informations au sujet du nom et de l'âge de John.

Essayez d'accéder à d'autres champs comme `john.age` ⬆️

Vous pouvez aussi accéder aux champs d'un _record_ en utilisant une « fonction d'accès à un champ » comme ceci :

{% repl %}
[
	{
		"add-decl": "john",
		"input": "john = { first = \"John\", last = \"Hobson\", age = 81 }",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m81\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	},
	{
		"input": ".last john",
		"value": "\u001b[93m\"Hobson\"\u001b[0m",
		"type_": "String"
	},
	{
		"input": "List.map .last [john,john,john]",
		"value": "[\u001b[93m\"Hobson\"\u001b[0m,\u001b[93m\"Hobson\"\u001b[0m,\u001b[93m\"Hobson\"\u001b[0m]",
		"type_": "List String"
	}
]
{% endrepl %}

Il est souvent utile de **mettre à jour** les valeurs d'un _record_ :

{% repl %}
[
	{
		"add-decl": "john",
		"input": "john = { first = \"John\", last = \"Hobson\", age = 81 }",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m81\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	},
	{
		"input": "{ john | last = \"Adams\" }",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m81\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Adams\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	},
	{
		"input": "{ john | age = 22 }",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m22\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	}
]
{% endrepl %}

Ces expressions peuvent se lire de la façon suivante : « Donnez-moi une nouvelle version de John dont le nom est Adams » ou « John mais âgé de 22 ans ».

Remarquez qu'un tout nouveau _record_ est créé à chaque mise-à-jour de John et que le _record_ original n'est pas écrasé. Elm rend cette opération efficace en partageant le maximum de contenu possible. Si vous mettez à jour un champ sur dix, le nouveau _record_ partagera les neuf valeurs qui n'ont pas été modifiées.

Une fonction pour mettre à jour d'âge ressemblerait à quelque chose comme cela :

{% repl %}
[
	{
		"add-decl": "celebrateBirthday",
		"input": "celebrateBirthday person =\n  { person | age = person.age + 1 }\n",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "{ a | age : number } -> { a | age : number }"
	},
	{
		"add-decl": "john",
		"input": "john = { first = \"John\", last = \"Hobson\", age = 81 }",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m81\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	},
	{
		"input": "celebrateBirthday john",
		"value": "{ \u001b[37mage\u001b[0m = \u001b[95m82\u001b[0m, \u001b[37mfirst\u001b[0m = \u001b[93m\"John\"\u001b[0m, \u001b[37mlast\u001b[0m = \u001b[93m\"Hobson\"\u001b[0m }",
		"type_": "{ age : number, first : String, last : String }"
	}
]
{% endrepl %}

Cette syntaxe de mise-à-jour des _records_ est omniprésente en Elm. Nous en verrons donc beaucoup d'autres exemples dans les sections suivantes.
