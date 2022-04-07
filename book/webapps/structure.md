# Structurer les applications

Comme nous l'évoquions page précédente, **tous les modules devraient être construits autour d'un type central.** Donc si nous construisons une application de gestion de blog, nous pouvons commencer par définir ce genre de modules :

- `Main`
- `Page.Home`
- `Page.Search`
- `Page.Author`

Chaque page dispose de son module dédié, qui utilise l'Architecture Elm avec son `Model`, ses fonctions `init`, `update`, `view`, et tout autre utilitaire dont il peut éventuellement avoir besoin. C'est une excellente base de travail sur laquelle nous pouvons ajouter de plus en plus de types et de fonctions… Et si d'aventure nous créons un nouveau type personnalisé autour duquel gravitent des fonctions associées, nous pouvons alors déplacer le tout dans un nouveau module dédié.

Avant de regarder quelques exemples en détail, il est important de bien comprendre un aspect stratégique majeur.


## N'anticipez pas trop

Nos modules de page n'anticipent pas grand chose de nos futurs besoins… Généralement, on évite de définir des modules qui peuvent être utilisés à différents endroits. Même si cela peut paraître contre-intuitif, il faut éviter de partager des fonctions prématurément.

Au début de ma carrière, j'échaffaudais toujours de grands plans sur la façon dont les choses fonctionneraient ensemble. “Les pages d'affichage et d'édition ont toutes les deux besoin de posts, donc je vais créer un module `Post` !” Mais assez rapidement, je réalisais que seule la page de visualisation nécessite une date de publication, que j'avais besoin de gérer le cache en édition différemment, que cela avait un impact sur le stockage côté serveur, etc. Donc je me retrouvais avec un type `Post` tentaculaire qui gérait beaucoup trop de choses qui n'avaient rien à voir entre elles, et qui finissait par porter préjudice aux deux pages en question.

Commencer simplement avec des “pages” permet d'identifier facilement les choses qui **se ressemblent** tout en étant **différentes**. L'état de l'art des interfaces graphiques ! Par conséquent, dans le cas de l'édition et de l'affichage d'un post, il est sûrement pertinent d'avoir deux types distincts, `EditablePost` et `ViewablePost`, chacun disposant de sa propre structure, de ses fonctions associées et autres décodeurs JSON. Et à ce moment là, sans doute devient-il encore plus pertinent de créer deux modules pour accueillir le code associé à chacun d'eux. Ou pas ! Pour le déterminer, le plus simple reste encore d'essayer et de regarder ce que ça donne.

C'est rendu trivial par le compilateur qui facilite énormément les grosses opérations de refactoring. Si on casse quelque chose qui impacte 20 fichiers, on répare l'erreur à la source et nos 20 fichiers sont corrigés.


## Exemples

Vous trouverez des exemples de mise en œuvre de cette façon de faire dans les projets libres suivants :

- [`elm-spa-example`](https://github.com/rtfeldman/elm-spa-example)
- [`package.elm-lang.org`](https://github.com/elm/package.elm-lang.org)


> ## Le choc des cultures
>
> Les développeurs venant du monde JavaScript ont tendance à avoir acquis certaines habitudes, attentes et craintes très spécifiques au langage. Elles sont sans doute justifiées dans ce contexte précis, mais peuvent aussi s'avérer préjudiciables une fois transposées à Elm.
>
>
> ### Instincts défensifs
>
> Dans [The Life of a File](https://youtu.be/XpDsk374LDE) (vidéo en anglais), je pointe quelques éléments du folklore JavaScript qui peuvent rapidement vous égarer en Elm :
>
> - ~~**“Préférez les fichiers courts.”**~~ En JavaScript, plus vos fichiers sont longs, plus vous avez de chances d'introduire une *mutation sournoise* qui sera très difficile à déboguer. En Elm, cela ne peut pas se produire ! Votre fichier peut faire 2000 lignes, ça n'arrivera jamais.
> - ~~**“Trouver l'architecture idéale dès le départ.”**~~ En JavaScript, le refactoring est extrêmement risqué. Dans beaucoup de situations, il est souvent moins coûteux de tout réécrire. En Elm, le refactoring est rapide et fiable ! Vous pouvez modifier plusieurs dizaines de fichiers d'un coup en toute confiance.
>
> Ces instincts défensifs vous protègent de problèmes qui n'existent pas en Elm. En avoir conscience est une chose, le prendre en compte est une autre paire de manches ; j'ai pu constater que beaucoup de développeurs JS se sentaient mal à l'aise quand leurs fichiers dépassaient la barre des 400, 600 ou 800 lignes de code. **Augmentez le nombre de lignes de vos fichiers Elm !** Observez jusqu'où vous pouvez aller. Essayez d'utiliser les commentaires pour structurer le code, de créer des fonctions utilitaires, mais gardez tout dans un seul fichier. Vous serez surpris des bénéfices que vous en tirerez !
>
>
> ### MVC
>
> Certains peuvent être tentés, en découvrant l'Architecture Elm, de créer des modules dédiés pour `Model`, `Update` et `View`… Ne faites surtout pas ça !
>
> Cette approche délimite mal les domaines de responsabilités applicatives. Que se passe t-il si la fonction `Post.estimatedReadTime` est appelée par `update` *et* `view` ? La fonction n'appartient ni à l'un ni à l'autre. Avons-nous besoin de créer un module `Utils` ? Peut-être avons-nous besoin d'une sorte de *contrôleur* ? Le code résultant va être difficile à naviguer, puisque placer la fonction devient une question [ontologique](https://fr.wikipedia.org/wiki/Ontologie) sur laquelle tous vos collègues auront un avis bien à eux. Qu'est-ce qu'un `estimatedReadTime` ? Est-ce que que cela concerne l'*Estimation* ou plutôt le *Temps* ?
>
> **Si vous construisez chaque module autour d'un type, vous ne vous confrontez quasiment jamais à ce genre de questions.** Vous avez un module `Page.Home` qui contient un `Model`, `update`, `view` et quelques fonctions utilitaires. Si au fil du temps vous ajoutez un type `Post`, puis quelques fonctions comme `estimatedReadTime` pour le manipuler, puis que ça commence à grossir un peu trop, vous pouvez créer un module `Post` et y déplacer le tout. En appliquant cette recette simple, vous vous épargnez tous les atermoiements sur ces questions de domaine de responsabilité des modules. La plupart du temps, le code résultant est d'ailleurs beaucoup plus clair.
>
>
> ### Composants
>
> Les développeurs venant de React s'attendent naturellement à ce que tout soit des composants. **Essayer de créer des composants est la garantie d'un désastre en Elm.** La raison principale est que les composants sont des objets :
>
> - composants = état local + méthodes
> - état local + méthodes = objets
>
> Ça serait franchement bizarre de commencer à utiliser Elm et de se demander “Comment je pourrais structurer mon application avec des objets ?” Les objets n'existent pas en Elm ! La communauté vous recommandera d'utiliser des [types personnalisés](types/custom_types.html) et des fonctions à la place.
>
> Penser en termes de composants vous encourage à créer des modules basés sur l'aspect visuel de votre application : “Il y a une sidebar, je dois créer un module `Sidebar`.” Ça serait tellement plus simple de créer une fonction `viewSidebar` et de lui passer les arguments nécessaires… Cette sidebar n'a d'ailleurs probablement aucun état, peut-être juste un ou deux champs que l'on pourra ajouter au `Model` qu'on a déjà. Vous saurez s'il y a besoin de déplacer le code lié à cette sidebar dans un module spécifique parce que vous aurez entretemps créé un type personnalisé et toute une batterie de fonctions associées !
>
> Retenez simplement qu'écrire une fonction `viewSidebar` ne **signifie pas** que vous avez besoin d'écrire une fonction `update` et un `Model` pour l'accompagner. Résistez à cet instinct, **écrivez juste la fonction utilitaire dont vous avez besoin**.
