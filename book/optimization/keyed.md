# `Html.Keyed`

À la page précédente, nous avons appris comment fonctionner le DOM virtuel et comment `Hml.Lazy` nous permet d'optimiser le rendu. Maintenant nous allons voir [`Html.Keyed`](https://package.elm-lang.org/packages/elm/html/latest/Html-Keyed/) pour l'optimiser encore plus.

Cette optimisation est particulièrement utile pour les listes de données de votre interface qui doit supporter **l'insertion**, **la suppression** et **le tri**.


## Le prolème

Nous avons une liste de tous les présidents des Étas-Unis. Et l'on peut l'ordonner par nom, [par niveau d'étude](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_education), [par revenu](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_net_worth), et [par lieu de naissance](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_home_state).
Quand l'algorithme de diff (décrit dans la page précédente) reçoit une longue liste, il l'a parcourt par paire :

- Il diff le 1er élément courant avec le 1er élément suivant.
- Il diff le 2ème élément courant avec le 2ème élément suivant.
- ...

Mais quand l'ordre de tri est changé, toutes les paires seront changées ! On alourdit donc le script pour changer le DOM alors que l'on pourrait seulement réorganiser seulement certains noeuds.

C'est le même problème avec l'insertion et la suppression. Si on retire le 1er élément de 100 éléments, tout va être décaler d'un élément et toutes les paires seront différentes. On a donc 99 diffs et une seule suppression. Ce n'est pas optimiser !


## La solution


La solution pour ces probèmes est [`Html.Keyed.node`](https://package.elm-lang.org/packages/elm/html/latest/Html-Keyed#node), qui permet en liant une entrée avec une "clé" de facilement différencier chaque élément.

Par exemple avec notre exemple sur les présidents, le code ressemblerait à :


```elm
import Html exposing (..)
import Html.Keyed as Keyed
import Html.Lazy exposing (lazy)

viewPresidents : List President -> Html msg
viewPresidents presidents =
  Keyed.node "ul" [] (List.map viewKeyedPresident presidents)

viewKeyedPresident : President -> (String, Html msg)
viewKeyedPresident president =
  ( president.name, lazy viewPresident president )

viewPresident : President -> Html msg
viewPresident president =
  li [] [ ... ]
```

Chaque noeud enfant est associé avec une clé. À la place de faire un diff sur des pairs, on peut faire un diff sur les clés !

Dorénavant le DOM virtuel peut reconnaître quand la liste est ordonnée. D'abord il associe chaque président avec sa clé. Puis il fait un diff de celles-ci. On utilise `lazy` pour chaque entrée optimisant notre code. Parfait ! Il peut ensuite comprendre comment réarranger les noeuds dans l'ordre souhaité. Avec la version "keyed" on alourdit beaucoup moins le rendu.

Réordonner nous aide à comprendre le fonctionnemenet, mais ce n'est pas le cas le plus commun qui nécessite cette optimisation. Les **noeuds avec clés sont très importants dans les cas d'insertion et de suppressions**. Quand vous retirer le 1er élément d'une liste de 100 éléments, utiliser des noeuds liés par clé unique permets au DOM virtuel de reconnaître cette action immédiatement. Et il ne réalise donc qu'une suppressions au lieu de 99 diffs.


## Résumé

Toucher au DOM est extraordinairement lent comparé aux autres calculs réalisés dans une application normale. Il faut **toujours utiliser `Html.Lazy` et `Html.Keyed` en premier lieu.** Je recommande de le vérifier avec le profiler autant que possible. Certains navigateurs fournissent une vue chronologique (timeline) de votre programme, [comme cela](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference). Elle donne un résumé de combien de temps est passé au chargement, au calcul, à l'interpretation, au repaint etc. Si vous voyez que 10% du temps est passé au calcul, vous pouvez faire votre code Elm deux foix plus rapide et ne pas voir de différence importantes. Alors que le simple ajout des noeuds "lazy" et "keyed" peuvent réduire une grosse partie des 90% restant en touchant moins au DOM !
