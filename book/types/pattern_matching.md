# Le Pattern Matching (ou *filtrage par motif*)

Nous venons d'apprendre à créer des [types personnalisés](/types/custom_types.html) avec le mot-clé `type` au travers d'un exemple de salon de discussion dont les usagers sont de type `User` :

```elm
type User
  = Regular String Int
  | Visitor String
```

Les usagers de type `Regular` ont un identifiant et un âge, tandis que ceux de type `Visitor` n'ont qu'un identifiant. Nous avons personnalisé notre type, mais comment pouvons-nous l'utiliser concrètement ?


## `case`

Si nous voulons une fonction permettant de choisir l'identifiant à afficher pour un `User` donné, nous avons besoin d'utiliser une expression `case..of` :

```elm
toName : User -> String
toName user =
  case user of
    Regular name age ->
      name

    Visitor name ->
      name

-- toName (Regular "Thomas" 44) == "Thomas"
-- toName (Visitor "kate95")    == "kate95"
```

L'expression `case..of` permet de distinguer chaque variante, de façon à pouvoir afficher le nom de Kate ou de Thomas quel que soit le statut de leur compte.

Et si nous essayons de passer des arguments invalides comme  `toName (Visitor "kate95")` ou `toName Anonymous`, le compilateur nous le signale immédiatement. Ce genre d'erreur mineure peut donc être réglé en quelques secondes, plutôt que d'attendre que le problème survienne en production et demande plus de temps pour le solutionner.


## Jokers

La fonction `toName` que nous venons de définir fonctionne très bien, mais la valeur `age` n'est pas utilisée. Pour signifier qu'une donnée associée n'est pas utilisée, il est courant d'utiliser un “joker” plutôt que de lui donner un nom :

```elm
toName : User -> String
toName user =
  case user of
    Regular name _ ->
      name

    Visitor name ->
      name
```

Le caractère `_` atteste que la donnée est présente, mais explicite le fait que personne ne l'utilise.
