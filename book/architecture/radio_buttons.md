# Boutons radio

---
#### Suivez l'exemple sur l'[éditeur en ligne](https://elm-lang.org/examples/radio-buttons).
---

Imaginez un site web concentré sur la lecture, comme ce guide ! Dans ces circonstances, il est désirable d'offrir le choix entre différentes tailles de police (petite, moyenne ou grande) pour que vos lecteurs puissent l'ajuster selon leurs préférences. Cela peut se traduire par le code HTML suivant :

```html
<fieldset>
  <label><input type="radio">Small</label>
  <label><input type="radio">Medium</label>
  <label><input type="radio">Large</label>
</fieldset>
```

Tout comme l'exemple des boîtes à cocher, ce code permet d'exposer un choix à l'utilisateur. L'usage de `<label>` offrant une plus grande surface sur laquelle l'utilisateur peut cliquer.

Comme d'habitude, nous commençons par la définition du modèle. Celui-ci est intéressant car il utilise une [union de types](../types/union_types.md) pour plus de fiabilité.

```elm
type alias Model =
  { fontSize : FontSize
  , content : String
  }

type FontSize = Small | Medium | Large
```

Une taille de police de type `FontSize` peut prendre exactement trois valeurs : `Small`, `Medium` et `Large`. Le champ `fontSize` ne peut donc prendre aucune autre valeur. En JavaScript, l'alternative est d'utiliser une valeur de type string ou number et de compter sur l'absence de coquilles ou d'erreurs. Rien ne vous empêche d'utiliser ces mêmes valeurs en Elm, au risque de s'exposer à des bugs inutilement.

> **Note :** Essayez de privilégier au maximum l'usage des unions de type pour vos modélisations. Le meilleur moyen d'éviter les états invalides reste de rendre impossible leur représentation !

Maintenant, il faut pouvoir mettre à jour le modèle dans la fonction `update` quand l'utilisateur choisit une taille de police différente via les boutons radio :

```elm
type Msg
  = SwitchTo FontSize

update : Msg -> Model -> Model
update msg model =
  case msg of
    SwitchTo newFontSize ->
      { model | fontSize = newFontSize }
```

Maintenant, il nous faut décrire comment afficher le modèle à l'écran. Tout d'abord, voici une version du code où la logique est répétée plusieurs fois au sein de le fonction `view` :

```elm
view : Model -> Html Msg
view model =
  div []
    [ fieldset []
        [ label []
            [ input [ type_ "radio", onClick (SwitchTo Small) ] []
            , text "Small"
            ]
        , label []
            [ input [ type_ "radio", onClick (SwitchTo Medium) ] []
            , text "Medium"
            ]
        , label []
            [ input [ type_ "radio", onClick (SwitchTo Large) ] []
            , text "Large"
            ]
        ]
    , section [] [ text model.content ]
    ]
```

Le code peut être factorisé dans des fonctions réutilisables (et non des composants !). Commençons par la répétition de logique des boutons radio.

```elm
view : Model -> Html Msg
view model =
  div []
    [ fieldset []
        [ radio (SwitchTo Small) "Small"
        , radio (SwitchTo Medium) "Medium"
        , radio (SwitchTo Large) "Large"
        ]
    , section [] [ text model.content ]
    ]

radio : msg -> String -> Html msg
radio msg name =
  label []
    [ input [ type_ "radio", onClick msg ] []
    , text name
    ]
```

Après factorisation, la fonction `view` est devenue bien plus lisible !

Si vous n'avez pas d'autres boutons radio à afficher sur la page, vous pouvez vous en tenir à ça. Si vous désirez en afficher plus, comme sur l'exemple ci-dessous où il est possible de choisir la couleur et la sérigraphie de la police, alors il possible de factoriser le code d'affichage pour un ensemble de boutons radio.

```elm
view : Model -> Html Msg
view model =
  div []
    [ viewPicker
        [ ("Small", SwitchTo Small)
        , ("Medium", SwitchTo Medium)
        , ("Large", SwitchTo Large)
        ]
    , section [] [ text model.content ]
    ]

viewPicker : List (String, msg) -> Html msg
viewPicker options =
  fieldset [] (List.map radio options)

radio : (String, msg) -> Html msg
radio (name, msg) =
  label []
    [ input [ type_ "radio", onClick msg ] []
    , text name
    ]
```

Ici, la fonction `view` appelle la fonction réutilisable `viewPicker` permettant d'afficher le choix des taille, couleur et sérigraphie de la police. Il est maintenant possible d'étendre cette fonction avec des arguments supplémentaires pour ajouter une classe à chaque `<fieldset>` :

```elm
viewPicker : String -> List (String, msg) -> Html msg
viewPicker pickerClass options =
  fieldset [ class pickerClass ] (List.map radio options)
```

Ou même une liste arbitraire d'attributs pour plus de flexibilité :

```elm
viewPicker : List (Attribute msg) -> List (String, msg) -> Html msg
viewPicker attributes options =
  fieldset attributes (List.map radio options)
```

Voire des attributs spécifiques pour chaque bouton radio pour une flexibilité maximum ! Les possibilités de configuration sont multiples simplement en ajoutant plus d'informations à un argument de fonction.


## Trop de réutilisation ?

Nous venons de voir plusieurs manières d'écrire le même code. Parmi ces manières, laquelle est considérée la plus *correcte* ? Une bonne heuristique de conception d'API consiste à **choisir la solution la plus simple répondant à vos besoins**. Testons cette heuristique pour quelques scenario : 

  1. La page ne contient qu'un seul bouton radio. Dans ce cas, pas besoin d'une solution configurable et réutilisable, il suffit de le créer et de l'afficher. La factorisation s'effectuant facilement en Elm, autant attendre le moment opportun où le besoin s'en ressent.

  2. La page contient quelques boutons radio, tous avec les mêmes contraintes de style. C'est le cas couvert par l'exemple de ce chapitre. C'est une bonne opportunité de factoriser le code d'affichage de ces boutons radio en de l'utiliser dans la fonction `view`. En l'absence de contraintes de style supplémentaires (comme des classes ou des attributs personnalisés), il est inutile de pousser la conception plus loin.

  3. La page contient plusieurs boutons radio, tous avec des contraintes de style différentes. Imaginons une interface de sélection flexible et configurable, où chaque élément *se ressemble*, mais dont les différences sont suffisamment prononcées pour nécessiter une distinction dans le code. Si vous vous retrouvez à surcharger la fonction `view` avec une liste d'arguments complexes, alors il est possible que la factorisation soit trop prononcée. Dans ce cas, il est préférable de séparer le traitement d'un affichage *similaire* en deux sous-fonctions avec du code *similaire*, mais dont la maintenance et la compréhension seront plus simples indépendamment.

Pour résumer, il n'y a pas de recette toute faite. La solution doit être adaptée aux besoins applicatifs, tout en restant la plus simple possible. Dans certains cas, elle demande de factoriser du code, dans d'autres de dupliquer des portions *similaires*. Ce n'est que par la pratique et l'expérience que l'intuition se construit, l'essentiel étant de favoriser la simplicité.
