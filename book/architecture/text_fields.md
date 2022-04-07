# Champs texte

Nous allons créer une application simple qui intervertit le contenu d'une zone de saisie de texte.

**Cliquez sur le bouton bleu** pour consulter le code de l'application. Regardez notamment le conseil affiché sur le mot-clé `type`.

<div class="edit-link"><a href="https://elm-lang.org/examples/text-fields">Edit</a></div>

```elm
import Browser
import Html exposing (Html, Attribute, div, input, text)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)



-- MAIN


main =
  Browser.sandbox { init = init, update = update, view = view }



-- MODEL


type alias Model =
  { content : String
  }


init : Model
init =
  { content = "" }



-- UPDATE


type Msg
  = Change String


update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }



-- VIEW


view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", value model.content, onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

Ce code est une légère variante de l'exemple précédent. Nous définissons le même squelette applicatif avec un modèle, quelques messages, la mise à jour du modèle dans la fonction `update` et l'affichage dans la fonction `view`. Les différences résident dans l'implémentation, dont nous allons détailler le contenu.


## Model

Commençons par le modèle. Nous aurons besoin de stocker le texte saisi par l'utilisateur, qui devra être affiché en inversé par la suite.

```elm
type alias Model =
  { content : String
  }
```

Cette fois, le modèle est représenté sous la forme d'un record, dont le champ `content` contiendra le texte saisi par l'utilisateur.

> **Note:** Quel est l'intérêt d'utiliser un record avec un seul champ plutôt qu'un `String` directement ? Afin de faciliter l'évolution du code quand l'application deviendra plus complexe. Il est en effet très simple d'étendre un record avec un nouveau champ.


## View

Maintenant que le modèle est défini, procédons à la création de la fonction `view` :

```elm
view : Model -> Html Msg
view model =
  div []
    [ input [ placeholder "Text to reverse", value model.content, onInput Change ] []
    , div [] [ text (String.reverse model.content) ]
    ]
```

Un élément `<div>` est généré avec deux enfants, dont le plus intéressant est le nœud `<input>` ayant les attributs suivants :

- `placeholder` est le texte affiché en l'absence de saisie
- `value` est la valeur courante de la saisie dans `<input>`
- `onInput` envoie un message à chaque changement dans la saisie

Par exemple, la saisie du mort "bard" produira les quatre messages suivants :

1. `Change "b"`
2. `Change "ba"`
3. `Change "bar"`
4. `Change "bard"`

Ces messages seront traités par la fonction `update`.


## Update

Ce programme ne définissant qu'un type de message, la fonction `update` n'a qu'un seul cas à traiter :

```elm
type Msg
  = Change String

update : Msg -> Model -> Model
update msg model =
  case msg of
    Change newContent ->
      { model | content = newContent }
```

La valeur du champ `content` du modèle est mise à jour à la réception du message de modification du nœud `<input>`. Par conséquent, à la saisie du texte  `bard`, les messages reçus par la fonction `update` produiront successivement les modèles suivants :

1. `{ content = "b" }`
2. `{ content = "ba" }`
3. `{ content = "bar" }`
4. `{ content = "bard" }`

Il est nécessaire de stocker l'information de manière explicite dans le modèle, auquel cas il ne serait pas possible d'inverser le texte dans la fonction `view`.

> **Exercice:** Regardez cet [exemple](https://elm-lang.org/examples/text-fields) et afficher la taille de `content` dans la fonction `view`. Pour cela, utilisez la fonction [`String.length`](https://package.elm-lang.org/packages/elm/core/latest/String#length) !
>
> **Note:** Pour plus d'informations concernant le fonctionnement de `Change`, rendez-vous à la section sur les [types personnalisés](/types/custom_types.html) et le [pattern matching](/types/pattern_matching.html).
> 
