# Parser les URLs

Dans la vraie vie, on veut que notre application montre des choses différentes selon les URLs :

- `/search`
- `/search?q=seiza`
- `/settings`

Oui, mais comment faire ? En utilisant [`elm/url`](https://package.elm-lang.org/packages/elm/url/latest/) pour parser ces chaînes brutes en structures de données en Elm. Le plus simple pour comprendre ce que fait ce paquet, c'est de regarder des exemples, alors faisons ça !

## Exemple n°1

Disons que nous avons un site d’art, et que nous voulons proposer les adresses suivantes :

- `/sujet/architecture`
- `/sujet/peinture`
- `/sujet/sculpture`
- `/blog/42`
- `/blog/123`
- `/blog/451`
- `/utilisateur/tom`
- `/utilisateur/sue`
- `/utilisateur/sue/commentaire/11`
- `/utilisateur/sue/commentaire/51`

On a donc des pages sur des sujets, des posts de blog, des pages de profil, et un moyen d’afficher un commentaire d’utilisateur en particulier. On pourra utiliser le module [`Url.Parser`](https://package.elm-lang.org/packages/elm/url/latest/Url-Parser) pour écrire le parseur d’URL suivant :

```elm
import Url.Parser exposing (Parser, (</>), int, map, oneOf, s, string)

type Route
  = Sujet String
  | Blog Int
  | Utilisateur String
  | Commentaire String Int

routeParser : Parser (Route -> a) a
routeParser =
  oneOf
    [ map Sujet       (s "sujet" </> string)
    , map Blog        (s "blog" </> int)
    , map Utilisateur (s "utilisateur" </> string)
    , map Commentaire (s "utilisateur" </> string </> s "commentaire" </> int)
    ]

-- /sujet/pottery        ==>  Just (Sujet "pottery")
-- /sujet/collage        ==>  Just (Sujet "collage")
-- /sujet/               ==>  Nothing

-- /blog/42              ==>  Just (Blog 42)
-- /blog/123             ==>  Just (Blog 123)
-- /blog/mosaic          ==>  Nothing

-- /utilisateur/tom/            ==>  Just (Utilisateur "tom")
-- /utilisateur/sue/            ==>  Just (Utilisateur "sue")
-- /utilisateur/bob/commentaire/42  ==>  Just (Commentaire "bob" 42)
-- /utilisateur/sam/commentaire/35  ==>  Just (Commentaire "sam" 35)
-- /utilisateur/sam/commentaire/    ==>  Nothing
-- /utilisateur/                ==>  Nothing
```

Le module `Url.Parser` permet, de manière très concise, de transformer complètement des URLs valides en données Elm !


## Exemple n°2

Maintenant, disons que nous avons un blog perso, et que les adresses suivantes sont valides :

- `/blog/12/une-breve-histoire-des-chaises`
- `/blog/13/un-septembre-sans-fin`
- `/blog/14/10-trucs-incroyables-sur-les-baleines`
- `/blog/`
- `/blog?q=baleines`
- `/blog?q=seiza`

Dans le cas présent, nous avons des posts de blog individuels et une vue d'ensemble du blog avec un paramètre de recherche optionnel. Il faut ajouter le module [`Url.Parser.Query`](https://package.elm-lang.org/packages/elm/url/latest/Url-Parser-Query) pour pouvoir écrire notre parser :

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

-- /blog/14/10-trucs-incroyables-sur-les-baleines  ==>  Just (BlogPost 14 "10-trucs-incroyables-sur-les-baleines")
-- /blog/14                                        ==>  Nothing
-- /blog/10-trucs-incroyables-sur-les-baleines     ==>  Nothing
-- /blog/                                          ==>  Just (BlogQuery Nothing)
-- /blog                                           ==>  Just (BlogQuery Nothing)
-- /blog?q=chabudai                                ==>  Just (BlogQuery (Just "chabudai"))
-- /blog/?q=baleines                               ==>  Just (BlogQuery (Just "whales"))
-- /blog/?query=baleines                           ==>  Just (BlogQuery Nothing)
```

Les opérateurs `</>` et `<?>` permettent d'écrire des parsers qui ressemblent fort aux URLs qu'ils cherchent à parser. Et grâce à `Url.Parser.Query`, on peut gérer les paramètres de recherche comme `?q=seiza`.

## Exemple n°3

On a maintenant un site de documentation avec des adresses comme ça :

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

Et hop, c’est bon pour les fragments !

## Synthèse

Maintenant qu’on a vu quelques parsers, regardons comment tout ça s’intègre dans un programme `Browser.application`. Plutôt que de se contenter d’enregistrer l’URL comme la dernière fois, si on essayait de la transformer en donnée utile et de l'afficher ?

```elm
TODO
```

Les nouveautés :
1. notre fonction `update` parse l’URL quand elle reçoit un message `UrlChanged` ;
2. notre fonction `view` montre des choses différentes selon les adresses !

Rien de bien compliqué. Tant mieux !

Bon, mais il se passe quoi avec 10 pages différentes ? Ou 20 ? Ou 100 ? On met tout dans notre `view` ? On va pas laisser tout ça dans un seul fichier, si ? Mais alors, combien de fichiers ? Dans quelle arborescende de répertoires ? C’est ce qu'on va voir dans la partie suivante !
