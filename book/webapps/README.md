# Applications web

Jusqu’à présent, nous avons créé des programmes Elm avec `Browser.element`, qui permet de contrôler un élément HTML au sein d’une application plus grande. C'est très bien pour _commencer_ à utiliser Elm au travail (comme expliqué [ici - lien en anglais](https://elm-lang.org/blog/how-to-use-elm-at-work)), mais ensuite ? Comment généraliser l’utilisation de Elm ?

Dans ce chapitre, nous allons voir comment créer une application web avec tout un tas de pages qui s'intègrent parfaitement les unes aux autres. Mais, avant ça, voyons comment contrôler une seule page.


## Contrôler le document

Désormais, nous allons démarrer notre programme avec [`Browser.document`](https://package.elm-lang.org/packages/elm/browser/latest/Browser#document) :

```elm
document :
  { init : flags -> ( model, Cmd msg )
  , view : model -> Document msg
  , update : msg -> model -> ( model, Cmd msg )
  , subscriptions : model -> Sub msg
  }
  -> Program flags model msg
```

Les arguments sont presque les mêmes que pour `Browser.element`, à l’exception de la fonction `view`. Plutôt que de retourner du `Html`, elle retourne maintenant un [`Document`](https://package.elm-lang.org/packages/elm/browser/latest/Browser#Document) de ce type :

```elm
type alias Document msg =
  { title : String
  , body : List (Html msg)
  }
```

Cela permet de contrôler la balise `<title>` et le `<body>` du document. Par exemple, selon les données téléchargées par notre application, peut-être que nous voudrons afficher un titre de page plus spécifique. Il suffira alors de le changer dans notre fonction `view` !


## Servir la page

Le compilateur produit du HTML par défaut ; il suffit donc de compiler le code comme ceci :

```bash
elm make src/Main.elm
```

Le résultat sera un fichier nommé `index.html` que nous pourrons servir comme n’importe quel autre fichier HTML. Cette méthode fonctionne très bien ! On peut néanmoins obtenir un peu plus de flexibilité en (1) compilant Elm vers Javascript et (2) créant un fichier HTML personnalisé. Pour ce faire, on lance le compilateur comme ceci :

```bash
elm make src/Main.elm --output=main.js
```

Cela va produire un fichier `main.js`, que l’on peut alors référencer dans un fichier HTML, comme ceci :

```html
<!DOCTYPE HTML>
<html>
<head>
  <meta charset="UTF-8">
  <title>Main</title>
  <link rel="stylesheet" href="mon-style.css">
  <script src="main.js"></script>
</head>
<body>
  <script>var app = Elm.Main.init();</script>
</body>
</html>
```

Ce fichier HTML est très simple. On charge ce dont on a besoin dans la balise `<head>` et on initialise le programme Elm dans le `<body>`. Le programme Elm s’occupe du reste et affiche l’application entière !

Quelle que soit la méthode choisie, on dispose maintenant d'un fichier HTML lisible par le navigateur. Pour donner accès à ce HTML, on peut utiliser des services gratuits comme [GitHub Pages](https://pages.github.com/) ou [Netlify](https://www.netlify.com/), ou bien monter son propre serveur sur un VPS avec un service comme [OVH](https://www.ovhcloud.com/fr/vps/) ou [Digital Ocean](https://m.do.co/c/c47faa1916d2). Peu importe, du moment qu'on peut rendre notre HTML accessible à un navigateur !

> **Note 1:** Créer un fichier HTML personnalisé est utile si on a besoin de contrôler les fichiers CSS. De nombreuses équipes utilisent des projets comme [`rtfeldman/elm-css`](https://package.elm-lang.org/packages/rtfeldman/elm-css/latest/) ou [`mdgriffith/elm-ui`](https://package.elm-lang.org/packages/mdgriffith/elm-ui/latest/) pour gérer leurs styles depuis Elm, mais si votre équipe utilise beaucoup de CSS déjà écrit, ou des préprocesseurs CSS, aucun problème : il suffit de référencer le fichier CSS final dans la balise `<head>` du fichier HTML.
>
> **Note 2:** Le lien Digital Ocean ci-dessus est sponsorisé : si vous l'utilisez pour créer un compte et vous abonner, nous obtenons 25 $ de crédit sur les frais d'hébergement de `elm-lang.org` et `package.elm-lang.org`.
