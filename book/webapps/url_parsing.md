# Parser les URLs

Dans une vraie application web, on veut que notre application montre des choses différentes selon les URLs :

- `/search`
- `/search?q=seiza`
- `/settings`

Oui, mais comment faire ? En utilisant [`elm/url`](https://package.elm-lang.org/packages/elm/url/latest/) pour transformer ces chaînes brutes en structures de données en Elm. Le plus simple pour comprendre ce que fait ce paquet, c'est de regarder des exemples, alors faisons ça !

## Exemple n°1

Disons que nous avons un site d'art, et que nous voulons proposer les adresses suivantes :

- `/topic/architecture`
- `/topic/painting`
- `/topic/sculpture`
- `/blog/42`
- `/blog/123`
- `/blog/451`
- `/user/tom`
- `/user/sue`
- `/user/sue/comment/11`
- `/user/sue/comment/51`

On a donc des pages sur des sujets, des articles de blog, des pages de profil, et un moyen d'afficher un commentaire d'utilisateur en particulier. On pourra utiliser le module [`Url.Parser`](https://package.elm-lang.org/packages/elm/url/latest/Url-Parser) pour écrire le parser d'URL suivant :

```elm
import Url.Parser exposing (Parser, (</>), int, map, oneOf, s, string)

type Route
  = Topic String
  | Blog Int
  | User String
  | Comment String Int

routeParser : Parser (Route -> a) a
routeParser =
  oneOf
    [ map Topic   (s "topic" </> string)
    , map Blog    (s "blog" </> int)
    , map User    (s "user" </> string)
    , map Comment (s "user" </> string </> s "comment" </> int)
    ]

-- /topic/pottery        ==>  Just (Topic "pottery")
-- /topic/collage        ==>  Just (Topic "collage")
-- /topic/               ==>  Nothing

-- /blog/42              ==>  Just (Blog 42)
-- /blog/123             ==>  Just (Blog 123)
-- /blog/mosaic          ==>  Nothing

-- /user/tom/            ==>  Just (User "tom")
-- /user/sue/            ==>  Just (User "sue")
-- /user/bob/comment/42  ==>  Just (Comment "bob" 42)
-- /user/sam/comment/35  ==>  Just (Comment "sam" 35)
-- /user/sam/comment/    ==>  Nothing
-- /user/                ==>  Nothing
```

Le module `Url.Parser` permet, de manière très concise, de transformer complètement des URLs valides en données Elm !


## Exemple n°2

Maintenant, disons que nous avons un blog personnel et que les adresses suivantes sont valides :

- `/blog/12/the-history-of-chairs`
- `/blog/13/the-endless-september`
- `/blog/14/whale-facts`
- `/blog/`
- `/blog?q=whales`
- `/blog?q=seiza`

Dans le cas présent, nous avons des articles de blog individuels et une vue d'ensemble du blog avec un paramètre de recherche optionnel. Il faut ajouter le module [`Url.Parser.Query`](https://package.elm-lang.org/packages/elm/url/latest/Url-Parser-Query) pour pouvoir écrire notre parser :

```elm
import Url.Parser exposing (Parser, (</>), (<?>), int, map, oneOf, s, string)
import Url.Parser.Query as Query

type Route
  = BlogPost Int String
  | BlogQuery (Maybe String)

routeParser : Parser (Route -> a) a
routeParser =
  oneOf
    [ map BlogPost  (s "blog" </> int </> string)
    , map BlogQuery (s "blog" <?> Query.string "q")
    ]

-- /blog/14/whale-facts  ==>  Just (BlogPost 14 "whale-facts")
-- /blog/14              ==>  Nothing
-- /blog/whale-facts     ==>  Nothing
-- /blog/                ==>  Just (BlogQuery Nothing)
-- /blog                 ==>  Just (BlogQuery Nothing)
-- /blog?q=chabudai      ==>  Just (BlogQuery (Just "chabudai"))
-- /blog/?q=whales       ==>  Just (BlogQuery (Just "whales"))
-- /blog/?query=whales   ==>  Just (BlogQuery Nothing)
```

Les opérateurs `</>` et `<?>` permettent d'écrire des *parsers* (analyseurs syntaxiques) qui ressemblent fortement aux URLs qu'ils cherchent à analyser. Et grâce à `Url.Parser.Query`, on peut gérer les paramètres de recherche comme `?q=seiza`.

## Exemple n°3

On a maintenant un site de documentation avec des adresses comme celles-ci :

- `/Basics`
- `/Maybe`
- `/List`
- `/List#map`
- `/List#filter`
- `/List#foldl`

On peut utiliser le parser [`fragment`](https://package.elm-lang.org/packages/elm/url/latest/Url-Parser#fragment) du module `Url.Parser` pour gérer ce type d'adresses :

```elm
type alias Docs =
  (String, Maybe String)

docsParser : Parser (Docs -> a) a
docsParser =
  map Tuple.pair (string </> fragment identity)

-- /Basics     ==>  Just ("Basics", Nothing)
-- /Maybe      ==>  Just ("Maybe", Nothing)
-- /List       ==>  Just ("List", Nothing)
-- /List#map   ==>  Just ("List", Just "map")
-- /List#      ==>  Just ("List", Just "")
-- /List/map   ==>  Nothing
-- /           ==>  Nothing
```

Maintenant nous pouvons également gérer les fragments d'URL !

## Synthèse

Maintenant qu'on a vu quelques parsers, regardons comment tout ça s'intègre dans un programme `Browser.application`. Plutôt que de se contenter d'enregistrer l'URL comme la dernière fois, si on essayait de la transformer en donnée utile et de l'afficher ?

```elm
TODO
```

Les nouveautés :
1. notre fonction `update` analyse l'URL quand elle reçoit un message `UrlChanged` ;
2. notre fonction `view` montre des choses différentes selon les adresses !

Rien de bien compliqué. Tant mieux !

Bon, mais il se passe quoi avec 10 pages différentes ? Ou 20 ? Ou 100 ? Doit-on tout mettre dans notre `view` ? Peut-on laisser tout ça dans un seul fichier ? Mais alors, combien de fichiers ? Dans quelle arborescence de répertoires ? C'est ce qu'on va voir dans la partie suivante !
