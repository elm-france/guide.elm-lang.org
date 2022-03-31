# Navigation

Nous venons de voir comment servir une seule page, mais mettons que nous soyons en train de faire un site comme `package.elm-lang.org`. Il se compose de nombreuses pages (par exemple : [search](https://package.elm-lang.org/), [README](https://package.elm-lang.org/packages/elm/core/latest/), [docs](https://package.elm-lang.org/packages/elm/core/latest/Maybe)) qui fonctionnent toutes différemment. Comment fonctionne-t-il ?


## Plusieurs pages

Pour faire simple, on pourrait servir plusieurs fichiers HTML. On va sur la page d'accueil ? On charge un HTML. On va sur la page de documentation de `elm/core` ? On charge un HTML. Sur la doc de `elm/json` ? On charge un HTML.

Avant Elm 0.19, c’est exactement ce que faisait le site `package.elm-lang.org`. Ça marche bien, c'est simple, mais ça a quelques inconvénients :

1. **Des écrans vides.** L’écran se vide et reste blanc le temps de charger le nouvel HTML. Ça serait bien d'avoir des transitions, plutôt !
2. **Requêtes redondantes.** Chaque paquet dispose d’un unique fichier `docs.json`, mais il est chargé à chaque fois qu’on va sur la page d'un module du paquet, comme [`String`](https://package.elm-lang.org/packages/elm/core/latest/String) ou [`Maybe`](https://package.elm-lang.org/packages/elm/core/latest/Maybe). Il faudrait pouvoir partager des données entre pages.
3. **Code redondant.** La page d'accueil et la documentation partagent de nombreuses fonctions, comme `Html.text` et `Html.div`. Est-ce qu'on pourrait partager ce code entre pages ?

Ces trois problèmes peuvent être résolus ! Il suffit de ne charger du HTML qu’une fois, et de se débrouiller avec les changements d'URL.


## Une seule page

Plutôt que de créer notre programme avec `Browser.element` ou `Browser.document`, nous allons utiliser [`Browser.application`](https://package.elm-lang.org/packages/elm/browser/latest/Browser#application) pour éviter de charger du HTML à chaque changement d'URL :

```elm
application :
  { init : flags -> Url -> Key -> ( model, Cmd msg )
  , view : model -> Document msg
  , update : msg -> model -> ( model, Cmd msg )
  , subscriptions : model -> Sub msg
  , onUrlRequest : UrlRequest -> msg
  , onUrlChange : Url -> msg
  }
  -> Program flags model msg
```

Ce programme ajoute des fonctionnalités à `Browser.document` dans trois scénarios importants.

**Quand l’application démarre**, `init` reçoit l'[`Url`][u] actuelle depuis la barre d'adresse du navigateur. Cela permet d'afficher des choses différentes selon l'`Url`.

**Quand quelqu’un clique sur un lien**, comme `<a href="/accueil">Accueil</a>`, le clic est intercepté et transformé en [`UrlRequest`][ur]. Plutôt que de charger du nouveau HTML, avec tous les inconvénients déjà mentionnés, `onUrlRequest` crée un message pour la fonction `update` qui permet de décider exactement ce que l'on souhaite faire. Cela peut être d’enregistrer la position du scroll, persister de la donnée, modifier l'URL programmatiquement, etc.

**Quand l’URL change**, la nouvelle `Url` est envoyée à `onUrlChange`. Le message résultant est envoyé à `update`, où l’on peut décider quoi montrer pour cette nouvelle page.

Ensemble, ces trois ajouts permettent d’avoir le contrôle complet sur les changements d'URL. Voyons ce que ça donne à l'usage !

[u]: https://package.elm-lang.org/packages/elm/url/latest/Url#Url
[ur]: https://package.elm-lang.org/packages/elm/browser/latest/Browser#UrlRequest
[bn]: https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation
[bnp]: https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation#pushUrl
[bnl]: https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation#load


## Exemple

Commençons avec le programme `Browser.application` le plus simple : il ne fait que garder trace de l'URL actuelle. Ne vous attardez pas sur le code : il n’y a rien de très intéressant, à part dans la fonction `update`, et nous la verrons en détail après le code.


```elm
import Browser
import Browser.Navigation as Nav
import Html exposing (..)
import Html.Attributes exposing (..)
import Url



-- MAIN


main : Program () Model Msg
main =
  Browser.application
    { init = init
    , view = view
    , update = update
    , subscriptions = subscriptions
    , onUrlChange = UrlChanged
    , onUrlRequest = LinkClicked
    }



-- MODEL


type alias Model =
  { key : Nav.Key
  , url : Url.Url
  }


init : () -> Url.Url -> Nav.Key -> ( Model, Cmd Msg )
init flags url key =
  ( Model key url, Cmd.none )



-- UPDATE


type Msg
  = LinkClicked Browser.UrlRequest
  | UrlChanged Url.Url


update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
  case msg of
    LinkClicked urlRequest ->
      case urlRequest of
        Browser.Internal url ->
          ( model, Nav.pushUrl model.key (Url.toString url) )

        Browser.External href ->
          ( model, Nav.load href )

    UrlChanged url ->
      ( { model | url = url }
      , Cmd.none
      )



-- SUBSCRIPTIONS


subscriptions : Model -> Sub Msg
subscriptions _ =
  Sub.none



-- VIEW


view : Model -> Browser.Document Msg
view model =
  { title = "URL Interceptor"
  , body =
      [ text "L'URL actuelle est : "
      , b [] [ text (Url.toString model.url) ]
      , ul []
          [ viewLink "/accueil"
          , viewLink "/profil"
          , viewLink "/reviews/the-century-of-the-self"
          , viewLink "/reviews/public-opinion"
          , viewLink "/reviews/shah-of-shahs"
          ]
      ]
  }


viewLink : String -> Html msg
viewLink path =
  li [] [ a [ href path ] [ text path ] ]
```

La fonction `update` traite des messages `LinkClicked` ou `UrlChanged`. Il y a beaucoup de choses nouvelles dans la branche `LinkClicked`, alors commençons par ça !


## `UrlRequest`

Quand on clique sur lien comme `<a href="/accueil">/accueil</a>`, cela produit une valeur de type `UrlRequest` :

```elm
type UrlRequest
  = Internal Url.Url
  | External String
```

La variante `Internal` s’applique aux liens qui restent dans le même domaine que l'application. Par exemple, si notre application est sur `https://exemple.fr`, les liens suivants sont des liens internes : `parametres#confidentialite`, `/accueil`, `https://exemple.fr/accueil`, et `//exemple.fr/home`.

La variante `External` concerne les liens qui pointent vers un domaine différent. Les liens `https://elm-lang.org/examples`, `https://static.exemple.fr`, and `http://exemple.fr/accueil` pointent tous vers un domaine différent. Notez que passer du protocole `https` à `http` est considéré comme un changement de domaine !

Quel que soit le type de lien cliqué, notre programme d'exemple va créer un message `LinkClicked` et l’envoyer à la fonction `update`. C'est là que se trouve le code le plus intéressant !


### `LinkClicked`

La majeure partie de la logique de notre `update` consiste à décider quoi faire des valeurs `UrlRequest` :

```elm
update : Msg -> Model -> ( Model, Cmd Msg )
update msg model =
  case msg of
    LinkClicked urlRequest ->
      case urlRequest of
        Browser.Internal url ->
          ( model, Nav.pushUrl model.key (Url.toString url) )

        Browser.External href ->
          ( model, Nav.load href )

    UrlChanged url ->
      ( { model | url = url }
      , Cmd.none
      )
```

Les fonctions `Nav.load` et `Nav.pushUrl` sont particulièrement intéressantes : elles sont toutes les deux issues du module [`Browser.Navigation`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation) qui contient tout un tas de manières de changer l'URL dans la barre d'adresse. Ici, nous utilisons les deux plus communes :

- [`load`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation#load) charge du nouveau HTML. C’est comme si on tapait l’URL dans la barre d'adresse et qu'on appuyait sur Entrée. Cela signifie que notre `Model` n'est pas conservé et qu'une toute nouvelle page est chargée.
- [`pushUrl`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation#pushUrl) change l’URL mais, au lieu de charger du nouveau HTML, déclenche un message `UrlChanged` auquel on peut réagir ! Cela ajoute également une nouvelle entrée dans l’historique de navigation, pour que l’application se comporte correctement avec les boutons `Précédent` et `Suivant`.

Si l’on revient à notre fonction `update`, on voit maintenant comment tout s’articule : quand l’utilisateur clique sur un lien `https://elm-lang.org`, on obtient un message `External` et on utilise `load` pour charger du nouveau HTML depuis le serveur demandé. Mais si l’utilisateur clique sur le lien `/accueil`, alors on obtient un message `Internal` et on utilise `pushUrl` pour modifier l'URL _sans_ charger du nouveau HTML.

> **Note 1:** Dans notre exemple, les liens `Internal` et `External` produisent tous les deux des commandes immédiatement, mais ce n'est pas obligatoire ! Quand une personne clique sur un lien `External`, on peut par exemple vouloir enregistrer le contenu d’un champ texte dans notre base de données avant qu’elle ne quitte la page. Ou bien, quand une personne clique sur un lien `Internal`, on peut vouloir utiliser [`getViewport`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Dom#getViewport) pour enregistrer la position de son défilement, au cas où elle clique sur `Précédent`. Tout est possible ! Il s’agit d'un message envoyé à la fonction `update`, et on peut faire tout ce qu’on veut avant de procéder au changement de page.
>
> **Note 2:** Si vous cherchez à afficher "ce que la personne regardait" au cas où elle clique sur `Précédent`, la position de son défilement n’est pas si utile que ça. Si la personne a redimensionné son navigateur ou changé l’orientation de son appareil, cela pourrait ne plus du tout correspondre ! Il vaut sans doute mieux chercher à enregistrer directement ce qu’elle était en train de regarder, par exemple en utilisant [`getViewportOf`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Dom#getViewportOf) pour savoir ce qui est affiché à l’écran à ce moment-là. Tout ça dépend beaucoup de votre application, difficile pour moi de donner un conseil plus précis !


## `UrlChanged`

Il existe plusieurs manières d’obtenir des messages `UrlChanged`. On vient de voir que `pushUrl` en produit, mais les boutons `Précédent` et `Suivant` du navigateur en produisent également. Par ailleurs, comme on vient de le voir plus haut, ce n’est pas parce qu'un message `LinkClicked` est reçu que la commande `pushUrl` va être appelée immédiatement.

C'est pourquoi il est très utile d’avoir un message `UrlChanged` indépendant : peu importe quand et par qui l'URL a changé, ce qui compte, c'est qu’elle a changé !

Dans notre exemple basique, on se contente de stocker la nouvelle URL dans le `Model`, mais dans une vraie application web, on devrait parser l'URL pour déduire la page à afficher. C’est ce dont nous allons parler ensuite !

> **Note:** Je n’ai pas mentionné [`Nav.Key`](https://package.elm-lang.org/packages/elm/browser/latest/Browser-Navigation#Key) pour nous concentrer sur les concepts importants, mais je vais en parler ici pour ceux que cela intéresse !
>
> Une `Key` (`Clef`) de navigation est requise pour pouvoir créer des commandes de navigation (comme `pushUrl`) qui modifient l’URL. La `Key` est obtenue uniquement lors de la création du programme via `Browser.application`, pour garantir que le programme est équipé pour détecter les changements d’URL. Si les `Key` étaient accessibles à d’autres programmes, les développeuses et développeurs se retrouveraient confrontés à des [bugs pénibles][bugs] et devraient se débrouiller tant bien que mal pour découvrir les bonnes techniques.
>
> Pour ces raisons, il faut garder une `Key` dans notre `Model`. C’est un prix plutôt faible à payer pour s’économiser toute une catégorie de problèmes complexes !

[bugs]: https://github.com/elm/browser/blob/1.0.0/notes/navigation-in-elements.md
