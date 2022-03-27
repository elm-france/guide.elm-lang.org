# Boutons radio

---
#### Suivez l'exemple sur l'[éditeur en ligne](https://elm-lang.org/examples/radio-buttons).
---

Imaginez un site web concentré sur la lecture, comme ce guide ! Dans ces circonstances, il est désirable d'offrir le choix entre différentes tailles de police (petite, moyenne ou grande) pour que vos lecteurs puissent l'ajuster selon leurs préférences. Ce qui peut se traduire par le code HTML suivant :

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

If that is the only chunk of radio buttons on your page, you are done. But perhaps you have a couple sets of radio buttons. For example, this guide not only lets you set font size, but also color scheme and whether you use a serif or sans-serif font. Each of those can be implemented as a set of radio buttons, so we could do a bit more refactoring, like this:

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

So if we want to let users choose a color scheme or toggle serifs, the `view` can reuse `viewPicker` for each case. If we do that, we may want to add additional arguments to the `viewPicker` function. If we want to be able to set a class on each `<fieldset>`, we could add an argument like this:

```elm
viewPicker : String -> List (String, msg) -> Html msg
viewPicker pickerClass options =
  fieldset [ class pickerClass ] (List.map radio options)
```

Or if we wanted even more flexibility, we could let people pass in whatever attributes they please, like this:

```elm
viewPicker : List (Attribute msg) -> List (String, msg) -> Html msg
viewPicker attributes options =
  fieldset attributes (List.map radio options)
```

And if we wanted even MORE flexibility, we could let people pass in attributes for each radio button too! There is really no end to what can be configured. You just add a bit more information to an argument.


## Too Much Reuse?

In this case, we saw quite a few ways to write the same code. But which way is the *right* way to do it? A good rule to pick an API is **choose the absolute simplest thing that does everything you need**. Here are some scenarios that test this rule:

  1. There is the only radio button thing on your page. In that case, just make them! Do not worry about making a highly configurable and reusable function for radio buttons. Refactoring is easy in Elm, so wait for a legitimate need before doing that work!

  2. There are a couple radio button things on your page, all with the same styling. That is how the options on this guide look. This is a great case for sharing a view function. You may not even need to change any classes or add any custom attributes. If you do not need that, do not design for it! It is easy to add later.

  3. There are a couple radio button things on your page, but they are very different. You could do an extremely flexible picker that lets you configure everything, but at some point, things that *look* similar are not actually similar enough for this to be worth it. So if you ever find yourself with tons of complex arguments configuring a view function, you may have overdone it on the reuse. I personally would prefer to have two chunks of *similar* view code that are both simple and easy to change than one chunk of view code that is complex and hard to understand.

Point is, there is no magic recipe here. The answer will depend on the particulars of your application, and you should always try to find the simplest approach. Sometimes that means sharing code. Sometimes it means writing *similar* code. It takes practice and experience to get good at this, so do not be afraid to experiment to find simpler ways!
