# Formulaires

Maintenant, construisons un formulaire basique comportant des champs pour le nom, le mot de passe et la vérification du mot de passe. Nous implémenterons une étape de validation qui vérifiera que les mots de passe saisis sont identiques.

Le code du programme est disponible ci-dessous. Vous pouvez cliquer sur le bouton bleu "Éditer" pour le modifier avec l'éditeur en ligne. Essayez d'introduire une coquille pour afficher des messages d'erreur. Par exemple, en saisissant incorrectement le champ `password` ou la fonction `placeholder`. **Allez-y, cliquez sur le bouton bleu !**

<div class="edit-link"><a href="https://elm-lang.org/examples/forms">Edit</a></div>

```elm
import Browser
import Html exposing (..)
import Html.Attributes exposing (..)
import Html.Events exposing (onInput)



-- MAIN


main =
  Browser.sandbox { init = init, update = update, view = view }



-- MODEL


type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }


init : Model
init =
  Model "" "" ""



-- UPDATE


type Msg
  = Name String
  | Password String
  | PasswordAgain String


update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }



-- VIEW


view : Model -> Html Msg
view model =
  div []
    [ viewInput "text" "Name" model.name Name
    , viewInput "password" "Password" model.password Password
    , viewInput "password" "Re-enter Password" model.passwordAgain PasswordAgain
    , viewValidation model
    ]


viewInput : String -> String -> String -> (String -> msg) -> Html msg
viewInput t p v toMsg =
  input [ type_ t, placeholder p, value v, onInput toMsg ] []


viewValidation : Model -> Html msg
viewValidation model =
  if model.password == model.passwordAgain then
    div [ style "color" "green" ] [ text "OK" ]
  else
    div [ style "color" "red" ] [ text "Passwords do not match!" ]
```

Ce programme est similaire à celui sur les [zones de texte](text_fields.md) mais avec plus de champs.


# Modèle

Commençons par le modèle. Sachant qu'il y aura trois champs textuels, nous pouvons définir :

```elm
type alias Model =
  { name : String
  , password : String
  , passwordAgain : String
  }
```

Il est préférable de démarrer avec un modèle minimal, même s'il ne contient qu'un seul champ, suivi de l'écriture des fonctions `view` et `update`. Ces étapes révèlent souvent les modifications supplémentaires à apporter au modèle. Cette construction progressive du modèle permet d'avoir un programme fonctionnel tout au long du développement. Toutes les caractéristiques ne sont pas encore présentes, mais chaque chose en son temps.

## Mise à jour du modèle

Nous avons souvent une bonne intuition de ce à quoi la mise à jour du modèle va ressembler. Nous aurons besoin de changer les valeurs des trois champs du modèle, ce qui nécessite la définition d'un message pour chacun.

```elm
type Msg
  = Name String
  | Password String
  | PasswordAgain String
```

La fonction `update` doit donc traiter chacune des trois variantes du message :

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Name name ->
      { model | name = name }

    Password password ->
      { model | password = password }

    PasswordAgain password ->
      { model | passwordAgain = password }
```

Chaque cas utilise la syntaxe de modification d'enregistrement pour transformer le champ du modèle concerné. Cela reste semblable à d'autres exemples vus précédemment, mais avec plus de cas.

Nous ferons plus dans l'originalité du coté de la fonction `view`.


## Vue

Notre fonction `view` utilise des **fonctions utilitaires** pour une meilleure organisation du code :

```elm
view : Model -> Html Msg
view model =
  div []
    [ viewInput "text" "Name" model.name Name
    , viewInput "password" "Password" model.password Password
    , viewInput "password" "Re-enter Password" model.passwordAgain PasswordAgain
    , viewValidation model
    ]
```

Dans les exemples précédents, nous utilisions `input` et `div` directement. Pourquoi faire différemment cette fois ?

Une caractéristique importante du HTML en Elm est que `input` et `div` sont de simples fonctions. Chacune prend (1) une liste de champs et (2) une liste de nœuds enfants. **Comme nous manipulons des fonctions Elm, nous pouvons utiliser toute la puissance de Elm à notre disposition pour construire nos vues !** Notamment, nous pouvons factoriser le code répétitif dans des fonctions séparées, comme nous allons le voir par la suite.

Notre fonction `view` procède à trois appels de la fonction `viewInput` :

```elm
viewInput : String -> String -> String -> (String -> msg) -> Html msg
viewInput t p v toMsg =
  input [ type_ t, placeholder p, value v, onInput toMsg ] []
```

Un appel tel que `viewInput "text" "Name" "Bill" Name` en Elm produira la valeur HTML `<input type="text" placeholder="Name" value="Bill">` une fois montré à l'écran.

Le quatrième appel à `viewValidation` est intéressant :

```elm
viewValidation : Model -> Html msg
viewValidation model =
  if model.password == model.passwordAgain then
    div [ style "color" "green" ] [ text "OK" ]
  else
    div [ style "color" "red" ] [ text "Passwords do not match!" ]
```

Cette fonction compare les deux mots de passe saisis. S'ils correspondent, un message de confirmation s'affiche en vert. S'ils diffèrent, un message d'erreur s'affiche en rouge.

Ces fonctions utilitaires montrent l'avantage d'avoir une bibliothèque HTML écrite en Elm. Il est possible d'intégrer ce code directement dans la fonction `view`, mais écrire des fonctions réutilisables est normal en Elm, même pour la partie visuelle. Si le code devient difficile à comprendre, peut-être vaut-il mieux le décomposer en plusieurs fonctions utilitaires.

> **Exercices:** Regardez cet [exemple](https://elm-lang.org/examples/forms) sur l'éditeur en ligne. Essayez d'ajouter les caractéristiques suivantes à la fonction utilitaire `viewValidation`:
>
>  - Vérifiez que le mot de passe saisi contient plus de 8 caractères.
>  - Assurez-vous que le mot de passe contienne un mélange de caractères majuscules, minuscules, et numériques.
>
> Utilisez les fonctionnalités du module [`String`](https://package.elm-lang.org/packages/elm/core/latest/String) pour résoudre ces exercices !
>
> **Attention:** Plus de connaissances sont nécessaires avant de commencer à envoyer des requêtes HTTP. Pour plus de facilité, nous vous recommandons de continuer la lecture jusqu'à la section HTTP avant de vous y essayer.
>
> **Note:** Aucune tentative d'implémentation d'une bibliothèque de validation générique ne s'est avérée concluante pour le moment. La logique de validation est souvent mieux exprimée par des fonctions Elm classiques adaptées au problème traité et retournant un résultat de type `Bool` ou `Maybe`. Commencez par là avant de choisir une solution plus complexe.
