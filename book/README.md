# Une introduction à Elm

**Elm est un langage fonctionnel compilé en Javascript.** Il va vous permettre de réaliser des sites web et des applications. Elm met fortement l'accent sur la simplicité et la qualité des outils.

Ce guide va :

  - Vous apprendre les fondamentaux de la programmation en Elm.
  - Vous montrer comment réaliser des applications interactives à l'aide de **l'Architecture Elm**.
  - Mettre l'accent sur des principes et des bonnes pratiques valables dans d'autres langages de programmation.

Suite à la lecture de ce guide, vous serez non seulement capable de créer de superbes applications web avec Elm, mais aussi de comprendre les idées et les concepts fondamentaux qui rendent Elm agréable à utiliser.

Si vous hésitez encore, je peux vous garantir que si vous donnez une chance à Elm et que vous l'utilisez pour réellement coder un projet, vous finirez quoiqu'il en soit par écrire un meilleur code JavaScript. Les idées sont facilement transférables !


## Un exemple concis

Voici un petit programme permettant d'incrémenter et de décrémenter un nombre :

```elm
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)

main =
  Browser.sandbox { init = 0, update = update, view = view }

type Msg = Increment | Decrement

update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1

view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

Essayez-le dans l'éditeur en ligne [ici](https://elm-lang.org/examples/buttons).

Le code peut sembler peu familier au premier abord, c'est pourquoi nous verrons bientôt comment fonctionne cet exemple !


## Pourquoi un *langage* fonctionnel?

Programmer dans un *style* fonctionnel présentent certains avantages, mais il y a des choses que vous ne pourrez obtenir que dans un *langage* fonctionnel comme Elm :

  - Aucune erreur d'exécution en pratique.
  - Des messages d'erreur conviviaux.
  - Une factorisation de code fiable.
  - Un versionnement sémantique automatique de tous les paquets Elm.

Aucune combinaison de bibliothèques JS ne peut vous donner toutes ces garanties car elles proviennent de la conception même du langage ! Et grâce à ces garanties, il est assez courant pour les programmeurs Elm de dire qu'ils ne se sont jamais sentis aussi **confiants** en programmant. Confiants pour ajouter des fonctionnalités rapidement. Confiants pour remanier des milliers de lignes. Mais sans l'angoisse de manquer quelque chose d'important !

J'ai mis l'accent sur la facilité d'apprentissage et d'utilisation d'Elm, donc tout ce que je vous demande, c'est d'essayer Elm et de voir ce que vous en pensez. J'espère que vous serez agréablement surpris !

> **Note :** Ce guide est le fruit d'un travail de traduction entrepris par la communauté [elm-france](https://elm-france.fr/) du [guide officiel](https://guide.elm-lang.org/) écrit par Evan Czaplicki. Les codes sources des guides [officiel](https://github.com/elm-guides/elm-lang.org) (en anglais) et [français](https://github.com/elm-france/guide.elm-lang.org) sont disponibles sous license [Creative Commons BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0).
