# `Html.Keyed`

À la page précédente, nous avons appris comment fonctionne le DOM virtuel et comment `Hml.Lazy` nous permet d'optimiser le rendu. Maintenant nous allons voir [`Html.Keyed`](https://package.elm-lang.org/packages/elm/html/latest/Html-Keyed/) pour l'optimiser encore plus.

Cette optimisation est particulièrement utile pour les listes de données de votre interface qui doivent prendre en charge **l'insertion**, **la suppression** et **le tri**.


## Le problème

Prenons une liste de l'ensemble des présidents des Étas-Unis qu'il est possible de trier par nom, [par niveau d'étude](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_education), [par revenu](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_net_worth), et [par lieu de naissance](https://en.wikipedia.org/wiki/List_of_Presidents_of_the_United_States_by_home_state).
Quand l'algorithme de comparaison (décrit dans la page précédente) reçoit une longue liste, il la parcourt par paire :

- Il compare le 1er élément courant avec le 1er élément suivant.
- Il compare le 2ème élément courant avec le 2ème élément suivant.
- ...

Mais quand l'ordre de tri est changé, toutes les paires le sont également ! Cela génère beaucoup d'opérations sur le DOM qu'il suffirait simplement de réorganiser certains noeuds.

Idem concernant l'insertion et la suppression. En retirant le 1er des 100 éléments, l'ensemble sera décalé d'un élément et toutes les paires seront différentes. La comparaison produira 99 différences et une suppression à la fin. Peut mieux faire !


## La solution


La solution à ces problèmes est d'utiliser [`Html.Keyed.node`](https://package.elm-lang.org/packages/elm/html/latest/Html-Keyed#node), qui permet en liant une entrée avec une "clé" de facilement différencier chaque élément du reste.

Sur l'exemple des présidents, le code ressemblerait à :

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

Chaque nœud enfant est associé à une clé. Il est donc possible d'effectuer des comparaisons par clé au lieu d'utiliser les paires.

Dorénavant, le DOM virtuel peut reconnaître quand l'ordre de la liste est changée. D'abord, chaque président est associé à sa clé, puis les clés sont comparées entre elles. En utilisant `lazy` pour chaque entrée, nous n'avons pas à nous préoccuper de tout ça. Parfait ! Il peut ensuite déterminer comment réarranger les nœuds dans l'ordre souhaité. Par conséquent, la version utilisant les clés demandera moins d'opérations.

Réordonner nous aide à comprendre le fonctionnement, mais ce n'est pas le cas le plus commun nécessitant vraiment cette optimisation. Les **nœuds avec clé sont particulièrement importants lors des insertions et des suppressions**. Lors du retrait du 1er des 100 éléments, utiliser des noeuds avec clé permet au DOM virtuel de reconnaître immédiatement cette action. Ce qui aura pour effet une seule suppression et non 99 comparaisons.


## Résumé

Manipuler le DOM est particulièrement lent comparé aux autres calculs réalisés dans une application normale. Il faut **toujours utiliser `Html.Lazy` et `Html.Keyed` en premier lieu.** Je recommande de le vérifier en profilant l'application le plus possible. Certains navigateurs fournissent une vue chronologique (timeline) de votre programme, [comme ici](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/reference). Elle donne une synthèse du temps passé au chargement, au calcul, à l'interpretation, au rendu, etc. Si vous constatez que 10% du temps est passé au calcul, vous pouvez rendre votre code Elm deux foix plus rapide sans constater de différences de performance importantes. Tandis que le simple ajout des nœuds "lazy" et "keyed" peut réduire une bonne partie des 90% restant en touchant moins au DOM !
