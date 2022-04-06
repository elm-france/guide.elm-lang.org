# Optimization

Il y deux types d'optimisation majeurs en Elm : l'optimisation de performance et l'optimisation de la taille des ressources.

- **Performance** &mdash; Le DOM est de loin ce qu'il y a de plus lent dans la navigateur. J'ai analysé beaucoup d'applications Elm afin de les accélérer, mais la plupart des solutions ont un impact limité. Utiliser de meilleures structures de données ? C'est négligeable. Mettre en cache des résultats de calcul dans le modèle ? C'est négligeable _et_ la qualité du code en pâtit. Seul l'usage de `Html.Lazy` et `Html.Keyed` a un impact significatif sur les performances en réduisant le nombre d'opérations sur le DOM.

- **Taille des ressources** &mdash; Le fonctionnement dans un navigateur implique de faire particulièrement attention aux temps de chargement. Plus la taille des ressources est réduite, plus leur chargement sera rapide sur mobile ou avec une connexion internet réduite. Cela aura vraisemblablement plus d'importance que n'importe quel autre type d'optimisation. Fort heureusement, le compilateur Elm est capable de rendre le code le plus compact possible sans effort supplémentaire.

Chacune de ces optimisations est importante. Ce chapitre en détaillera leur fonctionnement.
