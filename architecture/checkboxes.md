# Boîtes à cocher

---
#### Parcourez cet exemple avec l'[éditeur en ligne](https://elm-lang.org/examples/checkboxes).
---

Pour celles et ceux venant du monde JavaScript, vous vous demandez certainement **&ldquo;où sont passés mes composants ?&rdquo;** et &ldquo;comment se passe la communication parent-enfant entre eux ?&rdquo;. Ces questions sont centrales en JavaScript, mais les choses fonctionnent différemment en Elm. Elm étant un langage fonctionnel, **nous ne résonnons pas en terme de composants, mais en terme de fonctions.**


Votre application sera probablement amenée à exposer des éléments de configuration à l'utilisateur, comme l'envoi de notifications par courriel ou la lecture automatique de contenu vidéo. Cela peut se traduire par le code HTML suivant :

```html
<fieldset>
  <label><input type="checkbox">Email Notifications</label>
  <label><input type="checkbox">Video Autoplay</label>
  <label><input type="checkbox">Use Location</label>
</fieldset>
```

Ce code expose des éléments de configuration par le biais de boîtes à cocher (*checkbox* en anglais), l'usage de l'élément `<label>` offrant une plus grande surface d’interaction à l'utilisateur.

Voyons comment gérer ces interactions côté Elm. Tout d'abord, commençons par la définition du modèle. Celui-ci devra conserver l'état de la configuration sélectionnée par l'utilisateur :

```elm
type alias Model =
  { notifications : Bool
  , autoplay : Bool
  , location : Bool
  }
```

Ensuite, définissons les messages à traiter et la fonction de mise à jour du modèle. Voici une proposition :

```elm
type Msg
  = ToggleNotifications
  | ToggleAutoplay
  | ToggleLocation

update : Msg -> Model -> Model
update msg model =
  case msg of
    ToggleNotifications ->
      { model | notifications = not model.notifications }

    ToggleAutoplay ->
      { model | autoplay = not model.autoplay }

    ToggleLocation ->
      { model | location = not model.location }
```

Enfin, nous définirons la fonction d'affichage suivante :

```elm
view : Model -> Html Msg
view model =
  fieldset []
    [ label []
        [ input [ type_ "checkbox", onClick ToggleNotifications ] []
        , text "Email Notifications"
        ]
    , label []
        [ input [ type_ "checkbox", onClick ToggleAutoplay ] []
        , text "Video Autoplay"
        ]
    , label []
        [ input [ type_ "checkbox", onClick ToggleLocation ] []
        , text "Use Location"
        ]
    ]
```

L'implémentation est relativement simple, mais il y a de la répétition dans le code. Notamment, il est possible de rendre la fonction `view` plus lisible. Les habitué.e.s du JavaScript auront plutôt tendance à définir un nouveau composant de type &ldquo;boîte à cocher labellisé&rdquo;. En Elm, il est plus simple de recourir à des fonctions réutilisables. Voici ce que donne une tentative de factorisation de la fonction `view` :

```elm
view : Model -> Html Msg
view model =
  fieldset []
    [ checkbox ToggleNotifications "Email Notifications"
    , checkbox ToggleAutoplay "Video Autoplay"
    , checkbox ToggleLocation "Use Location"
    ]

checkbox : msg -> String -> Html msg
checkbox msg name =
  label []
    [ input [ type_ "checkbox", onClick msg ] []
    , text name
    ]
```

Dorénavant, `view` fait appel à une fonction utilitaire `checkbox` bien plus configurable. Celle-ci accepte deux arguments : le message produit à chaque clic sur la boîte à cocher et le texte à afficher à côté de celle-ci. Si nous décidons plus tard d'attribuer une classe commune à toutes les boîtes à cocher, alors il suffira de modifier la fonction `checkbox`. La factorisation de code dans des fonctions utilitaires est le principe de base permettant l'implémentation de **vues réutilisables**.


## Comparaison entre vues et composants réutilisables

Nous avons maintenant suffisamment d'informations pour comparer ces deux approches. Les vues réutilisables présentent des avantages significatifs par rapport aux composants :

  - **Ce sont des fonctions, tout simplement.** Il n'y a rien de spécial à rajouter. Les fonctions offrent suffisamment de puissance et sont très simples à créer, car elles font partie des fondamentaux du langage Elm.

  - **Pas de communication parent-enfant.** L'implémentation d'un composant &ldquo;boîte à cocher&rdquo; aurait nécessité un effort de synchronisation entre l'état du composant et celui du modèle. Cela peut amener des incohérences, comme le fait d'afficher la boîte des notifications cochée malgré un modèle indiquant leur désactivation. À l'inverse, l'utilisation des fonctions en Elm permet d'isoler la logique d'affichage, sans recourir à des modifications du modèle lui-même ou de sa mise à jour.

En Elm, il est toujours possible de factoriser le code d'affichage sans modifier l'architecture globale d'une application. Il suffit simplement d'écrire des fonctions plus petites. Nous le vérifierons sur d'autres exemples par la suite.
