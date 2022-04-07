# Les types

L'un des points forts de Elm est de garantir l'**absence d'erreurs à l'exécution** — ou, plus précisément, de rendre impossible la levée d'exceptions à l'exécution du programme. Le compilateur analyse très rapidement le code source pour étudier comment les valeurs circulent dans l'application. Si une valeur est utilisée de façon erronée, le compilateur vous en avertit au moyen d'un message d'erreur informatif. On appelle cela l'*inférence de types* : le compilateur devine quels *types* de valeurs passent par vos fonctions.

## Un exemple d'inférence de types

Le code suivant définit une fonction `toFullName` qui extrait le nom complet d'une personne sous la forme d'une chaîne de caractères :

```elm
toFullName person =
    person.firstName ++ " " ++ person.lastName

fullName =
    toFullName { fistName = "Hermann", lastName = "Hesse" }
```

Comme en JavaScript ou en Python, nous écrivons ici le code sans fioritures. Mais voyez-vous le bug ?

En JavaScript, le code équivalent retournerait `"undefined Hesse"`… Il n'y aurait même pas d'erreur ! Espérons que vos usagers vous préviendront quand ils rencontreront ce bug en production. À l'opposé, le compilateur Elm analyse le code et vous dit :


```
-- TYPE MISMATCH ---------------------------------------------------------- REPL

The 1st argument to `toFullName` is not what I expect:

3|     toFullName { fistName = "Hermann", lastName = "Hesse" }
                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
This argument is a record of type:

    { fistName : String, lastName : String }

But `toFullName` needs the 1st argument to be:

    { a | firstName : String, lastName : String }

Hint: Seems like a record field typo. Maybe firstName should be fistName?
elm-france
```

Le compilateur constate que `toFullName` reçoit un argument du mauvais *type*. Comme le suggère le message d'erreur, quelqu'un a par erreur tapé `fist` au lieu de `first`.

C'est déjà très utile d'avoir un assistant pour des erreurs simples comme celle-ci, mais c'est encore plus précieux quand vous avez des centaines de fichiers et de nombreuses personnes qui collaborent dessus. Quelle que soit la taille et la complexité du projet, le compilateur Elm vérifie que **tout** est correct en se basant simplement sur le code source.

Mieux vous comprendrez les types et le typage, plus le compilateur vous assistera efficacement. Poursuivons notre découverte !
