# Les modules

Elm propose des **modules** pour gérer proprement l’augmentation de la quantité de code. Au niveau le plus basique, les modules permettent de séparer le code en plusieurs fichiers.

## Création d'un module

Idéalement, les modules Elm sont construits autour d'un type central. Par exemple, le module `List` est consacré au type `List`. Imaginons que l’on cherche à construire un module autour du type `Post` pour notre site de blog. On peut écrire quelque chose du genre :

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

La seule nouvelle syntaxe ici est la première ligne `module Post exposing (Post, tempsDeLecture, encode, decoder)`. Cela signifie que le module est connu sous le nom de `Post` et que seule une partie de ses valeurs sont exposées à l’extérieur. Comme c'est écrit là, la fonction `nombreDeMots` n'est utilisable qu'à _l'intérieur_ du module `Post`. En Elm, masquer certaines fonctions d'un module est une technique très importante.

> **Note :** Quand on omet la déclaration de module, Elm utilisera celle-ci par défaut :
>
>```elm
module Main exposing (..)
```
>
> Ça facilite la vie aux débutants en Elm qui ne travaillent que dans un fichier. On ne va pas les embêter avec le système de module dès leur premier jour, les pauvres !


## Faire grossir ses modules

Au fur et à mesure que votre application gagnera en complexité, vous ajouterez du code dans vos modules. C'est parfaitement normal pour des modules Elm de faire de 400 à 1 000 lignes, comme je l'explique dans [The Life of a File (en anglais)](https://youtu.be/XpDsk374LDE). Mais, quand on a plusieurs modules, comment choisir _dans lequel_ ajouter ce code ?

J’applique le raisonnement suivant, selon si le code en question est :

- **Spécifique** &mdash; Si la logique n’apparaît qu’à un seul endroit, je crée une fonction utilitaire que j’écris aussi près que possible de l’endroit où elle est utilisée. Éventuellement, j’ajoute un en-tête en commentaire, du genre `-- APERÇU D’UN POST` pour clarifier que les fonctions suivantes sont utilisées pour l’aperçu d'un post.
- **Similaire** &mdash; Disons qu’on veut montrer l'aperçu des `Post`s sur la page d'accueil et sur les pages des auteurs. Sur la page d’accueil, on va vouloir mettre le contenu en avant, avec des extraits plus longs. Sur une page d’auteur, par contre, on va vouloir insister sur la diversité des sujets, et insister sur les titres. Ces deux situations sont _similaires_, mais différentes : dans ce cas-là, on les traite comme du code _spécifique_ et on écrit le code à part.
- **Identique** &mdash; À un moment, on va avoir tout un tas de code **unique**. Tout va bien ! Mais on va peut-être se rendre compte que certaines fonctions contiennent de la logique qui est _identique_. C'est le moment parfait d'extraire une fonction utilitaire pour cette logique. Si cette logique n'est utilisée que dans ce module, il n'y a rien d'autre à faire. Éventuellement, ajoutez un en-tête en commentaire pour dire `-- TEMPS DE LECTURE`, si vous y tenez.

Ces techniques se limitent à créer des fonctions utilitaires dans un seul fichier. Ce n’est que quand tout un tas de fonctions utilitaires tournent autour d’un seul type qu'on va les mettre dans un nouveau module. Par exemple, on commence par créer un module `Page.Auteur` et on na va pas créer le module `Post` jusqu'à ce que les fonctions utilitaires commencent à s’empiler. À ce moment-là, la création d'un nouveau module devrait rendre le code plus simple à comprendre et à explorer. Si ce n’est pas le cas, il faut revenir à la version d'avant : avoir plus de modules ne donne pas forcément un code de meilleure qualité ! Choisissez l'organisation qui donne le code le plus simple et le plus clair.

Pour résumer, partez du principe que du code **similaire** est **unique**. (C’est d'ailleurs souvent le cas, dans les UIs, au final.) Si vous voyez de la logique qui est **identique** dans plusieurs fonctions, vous pouvez extraire des fonctions utilitaires, avec des en-têtes de commentaires. Quand vous atteignez un certain nombre de ces fonctions utilitaires centrées autour d’un type en particulier, _envisagez_ de les déplacer dans un nouveau module. Si ce nouveau module rend votre code plus clair, super ! Si non, faites machine arrière. Avoir plus de fichiers ne rend pas en soi le code plus simple ou plus clair.

> **Note :** À l’usage, l’utilisation des modules se révèle parfois piégeuse, dans la situation où du code qui était auparavant **identique** évolue pour ne devenir que **similaire**. C’est très courant, surtout pour des UIs ! Spontanément, les gens ont tendance à créer des fonctions "Frankenstein" qui sont capables de gérer tous les cas possibles. À ajouter des arguments. À ajouter des arguments plus _complexes_. Dans ces cas-là, il vaut mieux accepter qu'on a désormais deux situations **uniques** et de copier le code aux deux endroits, de le modifier pour qu'il corresponde exactement au besoin, et de voir si le code résultant est **identique**. Si oui, on le déplace dans une fonction utilitaire. **Les fonctions "trop longues" ont vocation à être décomposées en plusieurs fonctions plus petites, pas à devenir encore plus grosses et complexes.


## Utiliser les modules

Typiquement, en Elm, le code se trouve dans le répertoire `src/`. C’est même la valeur par défaut dans [`elm.json`](https://github.com/elm/compiler/blob/0.19.0/docs/elm.json/application.md). Donc notre module `Post` vivra dans un fichier appelé `src/Post.elm`. Après ça, on peut `import`er un module et utiliser ses valeurs exposées. Cela peut se faire de quatre manières différentes :

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

On recommande généralement de n’utiliser `exposing` que très rarement. Dans l'idéal, pour zéro ou un import. Sinon, ça commence à devenir compliqué de savoir d'où viennent les choses quand on lit le code : « Euh, `filtrerLesPostsPar`, ça vient d'où déjà ? Ça prend quoi, comme arguments ? ». Plus on utilise `exposing`, moins le code est facile à lire. J'ai tendance à l'utiliser pour `import Html exposing (..)`, mais c'est tout. Pour tout le reste, je recommande d’utiliser l’`import` standard et, éventuellement, d’utiliser `as` si le nom du module est particulièrement long.
