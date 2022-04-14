# Boutons

Notre premier exemple est un compteur qui peut être incrémenté ou décrémenté.

La totalité du programme est disponible ci-dessous. Cliquez sur le bouton bleu "Éditer" pour jouer avec l'éditeur en ligne. Essayez de changer le texte sur un des boutons.
**Cliquez maintenant sur le bouton bleu !**

<div class="edit-link"><a href="https://elm-lang.org/examples/buttons">Éditer</a></div>

```elm
import Browser
import Html exposing (Html, button, div, text)
import Html.Events exposing (onClick)



-- MAIN


main =
  Browser.sandbox { init = init, update = update, view = view }



-- MODEL

type alias Model = Int

init : Model
init =
  0


-- UPDATE

type Msg = Increment | Decrement

update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1


-- VIEW

view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

Une fois familiarisés avec le code, nous devrions nous poser quelques questions. Que fait la valeur `main` ? Comment tout cela fonctionne ? Parcourons le code afin d'en discuter.

> **Note:** Ce code d'exemple utilise les [annotations de type](/types/reading_types.html), les [alias](/types/type_aliases.html), et les [types personnalisés](/types/custom_types.html). Cette section ne fait qu'esquisser les principes de l'Architecture Elm, que nous aborderons plus en détail par la suite. Cela dit, rien ne vous empêche de creuser dès à présent ces aspects si le cœur vous en dit !

## Le point d'entrée -- `main`

La valeur `main` est spéciale en Elm. Elle décrit ce qui sera affiché à l'écran. En l'occurrence, l'application sera initialisée avec la valeur `init`, la fonction `view` affichera tout ce qu'il y a à afficher à l'écran, et les entrées utilisateur seront transmises à la fonction `update`. C'est en quelque sorte la description générale de notre programme.

## Modèle

La modélisation des données est extrêmement importante en Elm. L'intérêt du **modèle** est de projeter tous les détails de votre application sous forme de données.

Pour réaliser un compteur, nous devons garder trace d'un nombre qui augmente ou diminue. Notre modèle est vraiment simple cette fois-ci :

```elm
type alias Model = Int
```

Nous avons simplement besoin d'une valeur entière de type `Int` pour stocker la valeur courante du compteur :

```elm
init : Model
init =
  0
```

La valeur initiale est zéro, puis elle augmentera ou diminuera au fur et à mesure que les utilisateurs cliqueront sur les différents boutons.

## Vue

Maintenant que nous avons un modèle, il faut pouvoir l'afficher à l'écran. C'est le rôle de la fonction `view` :

```elm
view : Model -> Html Msg
view model =
  div []
    [ button [ onClick Decrement ] [ text "-" ]
    , div [] [ text (String.fromInt model) ]
    , button [ onClick Increment ] [ text "+" ]
    ]
```

Cette fonction prend le `model` en paramètre et renvoie du HTML. Nous déclarons vouloir afficher :

- un bouton pour décrémenter le compteur,
- la valeur courante du compteur,
- un bouton pour incrémenter le compteur.

Remarquez l'emploi du gestionnaire d'événements `onClick` pour chaque bouton ; cela veut dire : **quand quelqu'un clique, envoie un message**. Donc, le bouton _plus_ envoie le message `Increment`. Quel est ce message et où va t-il ? À la fonction `update`.

## Mise à jour du modèle

La fonction `update` décrit la façon dont le modèle va changer au fil du temps.

Nous avons défini deux messages qu'elle pourra recevoir :

```elm
type Msg = Increment | Decrement
```

À partir de là, la fonction `update` décrit simplement ce qu'il faut faire lorsqu'elle reçoit un de ces messages.

```elm
update : Msg -> Model -> Model
update msg model =
  case msg of
    Increment ->
      model + 1

    Decrement ->
      model - 1
```

À la réception d'un message de type `Increment`, le modèle est incrémenté. À la réception d'un message de type `Decrement`, le modèle est décrémenté.

À chaque fois qu'un message est reçu, la fonction `update` traite ce message et génère un nouveau modèle. Ensuite, nous appelons la vue afin d'afficher ce nouveau modèle à l'écran. Et ainsi de suite ! Une interaction utilisateur envoie un message, la fonction `update` met à jour le modèle, la fonction `view` l'affiche à l'écran. Etc.

## En résumé

Maintenant que nous avons abordé toutes les parties d'un programme Elm, il est désormais plus facile de comprendre comment celles-ci s'intègrent dans le diagramme vu précédemment :

![Diagram of The Elm Architecture](buttons.svg)

Elm commence par afficher la valeur initiale à l'écran. Ensuite, vous entrez dans cette boucle :

1. En attente d'une entrée utilisateur.
2. Envoie un message à la fonction `update`
3. Produit un nouveau `Model`
4. Appelle `view` pour obtenir un nouveau code HTML
5. Affiche le nouveau code HTML à l'écran
6. Et on boucle !

C'est l'essence même de l'Architecture Elm. Chaque exemple que nous verrons à partir de maintenant sera une légère variation de ce modèle.

> **Exercice :** Ajoutez un bouton pour réinitialiser le compteur à zéro :
>
> 1. Ajoutez une nouvelle variante `Reset` au type `Msg`
> 2. Ajoutez une branche `Reset` dans la fonction `update`
> 3. Ajoutez un bouton dans la fonction `view`.
>
> Vous pouvez éditer l'exemple dans l'éditeur en ligne [ici](https://elm-lang.org/examples/buttons).
>
> Pour aller plus loin, ajoutez un bouton permettant d'incrémenter le compteur de 10.
