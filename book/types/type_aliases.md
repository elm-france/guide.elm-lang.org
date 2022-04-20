# Les alias de type

Les annotations peuvent devenir très longues, particulièrement si vous avez des [*records*](/bases_du_langage.html#records) contenant de nombreux champs. C'est pour répondre à ce problème que les *alias* ont été introduits. Un **alias de type** est en quelque sorte un diminutif pour ce dernier, permettant de le référencer avec un nom plus court.

Par exemple, vous pouvez créer un alias `User` de cette façon :

```elm
type alias User =
  { name : String
  , age : Int
  }
```

Plutôt que d'écrire le type du _record_ complet systématiquement, il suffit de le référencer avec `User` pour obtenir des annotations beaucoup plus concises :

```elm
-- AVEC ALIAS

isOldEnoughToVote : User -> Bool
isOldEnoughToVote user =
  user.age >= 18


-- SANS ALIAS

isOldEnoughToVote : { name : String, age : Int } -> Bool
isOldEnoughToVote user =
  user.age >= 18
```

Ces deux déclarations sont équivalentes, mais celle utilisant un alias — plus courte — est plus facile à lire. Ici, nous utilisons simplement un **alias** qui se substitue à un type verbeux.


## Modèles

Il est très courant d'utiliser des alias de type pour définir un modèle. Dans la section sur l'[Architecture Elm](/architecture/), nous avions utilisé le `Model` suivant :

```
type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }
```

Les alias de type se révèlent particulièrement intéressants pour écrire les annotations des fonctions `update` et `view` : écrire `Msg -> Model -> Model` est tellement plus simple et confortable que de recopier la forme verbeuse du _record_ correspondant. En prime, la définition du modèle peut-être mise à jour sans nécessiter de corriger les annotations.


## Constructeurs de _record_

Quand vous créez un alias de _record_, cela génère du même coup un **constructeur de _record_**. Si vous définissez un alias de type `User`, vous pouvez construire les enregistrements correspondants comme ceci :

{% replWithTypes %}
[
	{
		"add-type": "User",
		"input": "type alias User = { name : String, age : Int }"
	},
	{
		"input": "User",
		"value": "\u001b[36m<function>\u001b[0m",
		"type_": "String -> Int -> User"
	},
	{
		"input": "User \"Sue\" 58",
		"value": "{ \u001b[37mname\u001b[0m = \u001b[93m\"Sue\"\u001b[0m, \u001b[37mage\u001b[0m = \u001b[95m58\u001b[0m }",
		"type_": "User"
	},
	{
		"input": "User \"Tom\" 31",
		"value": "{ \u001b[37mname\u001b[0m = \u001b[93m\"Tom\"\u001b[0m, \u001b[37mage\u001b[0m = \u001b[95m31\u001b[0m }",
		"type_": "User"
	}
]
{% endreplWithTypes %}

Essayez de créer un nouvel enregistrement `User`, puis de créer votre propre alias ⬆️

Remarquez que l'ordre des arguments passés au constructeur de _record_ correspond à l'ordre des champs dans l'alias de type !

Encore une fois, ceci n'est **valable que pour les _records_.** Créer des alias pour d'autres types ne fournira pas de constructeur.
