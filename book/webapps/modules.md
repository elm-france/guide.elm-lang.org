# Les modules

Elm a des **modules** pour gérer proprement l'augmentation de la quantité de code. Pour faire simple, les modules permettent de séparer le code en plusieurs fichiers.

## Création d'un module

Idéalement, les modules Elm sont construits autour d'un type central. Par exemple, le module `List` est consacré au type `List`. Disons que l'on cherche à construire un module autour du type `Post` pour notre site de blog. On peut écrire quelque chose du genre :

```elm
module Post exposing (Post, tempsDeLecture, encode, decoder)

import Json.Decode as D
import Json.Encode as E


-- POST

type alias Post =
  { titre : String
  , auteur : String
  , contenu : String
  }


-- READ TIME

tempsDeLecture : Post -> Float
tempsDeLecture post =
  toFloat (nombreDeMots post) / 220

nombreDeMots : Post -> Int
nombreDeMots post =
  List.length (String.words post.contenu)


-- JSON

encode : Post -> E.Value
encode post =
  E.object
    [ ("titre", E.string post.titre)
    , ("auteur", E.string post.auteur)
    , ("contenu", E.string post.contenu)
    ]

decoder : D.Decoder Post
decoder =
  D.map3 Post
    (D.field "titre" D.string)
    (D.field "auteur" D.string)
    (D.field "contenu" D.string)
```

La seule nouvelle syntaxe ici est la ligne `module Post exposing (Post, tempsDeLecture, encode, decoder)` tout en haut. Cela signifie que le module est connu sous le nom de `Post` et que seule une partie de ses valeurs sont exposées à l'extérieur. Comme c'est écrit là, la fonction `nombreDeMots` n'est utilisable qu'à _l'intérieur_ du module `Post`. En Elm, masquer certaines fonctions d'un module est une technique très importante.

> **Note :** Quand on omet la déclaration de module, Elm utilisera celle-ci par défaut :
>
>```elm
module Main exposing (..)
```
>
> Ça facilite la vie aux débutants en Elm qui ne travaillent que dans un fichier. On ne va pas les embêter avec le système de module dès leur premier jour, les pauvres !


## Growing Modules

Au fur et à mesure que votre application gagnera en complexité, vous ajouterez du code dans vos modules. C'est parfaitement normal pour des modules Elm de faire de 400 à 1 000 lignes, comme je l'explique dans [The Life of a File (en anglais)](https://youtu.be/XpDsk374LDE). Mais, quand on a plusieurs modules, comment choisir _dans lequel_ ajouter ce code ?

J'applique le raisonnement suivant, selon si le code en question est :

- **Unique** &mdash; Si la logique n'apparaît qu'à un seul endroit, je crée une fonction utilitaire que j'écris aussi près que possible de l'endroit où elle est utilisée. Éventuellement, j'ajoute un en-tête dans les commentaires, du genre `-- APERÇU D'UN POST` pour clarifier que les fonctions suivantes sont utilisées pour l'aperçu d'un post.
- **Similar** &mdash; Say we want to show `Post` previews on the home page and on the auteur pages. On the home page, we want to emphasize the interesting contenu, so we want longer snippets. But on the auteur page, we want to emphasize the breadth of contenu, so we want to focus on titres. These cases are _similar_, not the same, so we go back to the **unique** heuristic. Just write the logic separately.
- **The Same** &mdash; At some point we will have a bunch of **unique** code. That is fine! But perhaps we find that some definitions contain logic that is _exactly_ the same. Break out a helper function for that logic! If all the uses are in one module, no need to do anything more. Maybe put a comment header like `-- READ TIME` if you really want.

These heuristics are all about making helper functions within a single file. You only want to create a new module when a bunch of these helper functions all center around a specific custom type. For example, you start by creating a `Page.auteur` module, and do not create a `Post` module until the helper functions start piling up. At that point, creating a new module should make your code feel easier to navigate and understand. If it does not, go back to the version that was clearer. More modules is not more better! Take the path that keeps the code simple and clear.

To summarize, assume **similar** code is **unique** by default. (It usually is in user interfaces in the end!) If you see logic that is **the same** in different definitions, make some helper functions with appropriate comment headers. When you have a bunch of helper functions about a specific type, _consider_ making a new module. If a new module makes your code clearer, great! If not, go back. More files is not inherently simpler or clearer.

> **Note:** One of the most common ways to get tripped up with modules is when something that was once **the same** becomes **similar** later on. Very common, especially in user interfaces! Folks will often try to create a Frankenstein function that handles all the different cases. Adding more arguments. Adding more _complex_ arguments. The better path is to accept that you now have two **unique** situations and copy the code into both places. Customize it exactly how you need. Then see if any of the resulting logic is **the same**. If so, move it out into helpers. **Your long functions should split into multiple smaller functions, not grow longer and more complex!**


## Utiliser les modules

Typiquement, en Elm, le code se trouve dans le répertoire `src/`. C'est même la valeur par défaut dans [`elm.json`](https://github.com/elm/compiler/blob/0.19.0/docs/elm.json/application.md). Donc notre module `Post` vivra dans un fichier appelé `src/Post.elm`. Après ça, on peut `import`er un module et utiliser ses valeurs exposées. Cela peut se faire de quatre manières différentes :

```elm
import Post
-- Post.Post, Post.tempsDeLecture, Post.encode, Post.decoder

import Post as P
-- P.Post, P.tempsDeLecture, P.encode, P.decoder

import Post exposing (Post, tempsDeLecture)
-- Post, tempsDeLecture
-- Post.Post, Post.tempsDeLecture, Post.encode, Post.decoder

import Post as P exposing (Post, tempsDeLecture)
-- Post, tempsDeLecture
-- P.Post, P.tempsDeLecture, P.encode, P.decoder
```

On recommande généralement de n'utiliser `exposing` que très rarement. Dans l'idéal, pour zéro ou un import. Sinon, ça commence à devenir compliqué de savoir d'où viennent les choses quand on lit le code : « Euh, `filtrerLesPostsPar`, ça vient d'où déjà ? Ça prend quoi, comme arguments ? ». Plus on utilise `exposing`, moins le code est facile à lire. J'ai tendance à l'utiliser pour `import Html exposing (..)`, mais c'est tout. Pour tout le reste, je recommande d'utiliser l'`import` standard et, éventuellement, d'utiliser `as` si le nom du module est particulièrement long.