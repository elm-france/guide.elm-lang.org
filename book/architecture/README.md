# L'Architecture Elm

L'architecture Elm — The Elm Architecture (TEA) en anglais — est une façon d'architecturer des programmes interactifs comme des applications web ou des jeux.

Cette architecture semble émerger naturellement avec Elm. Plutôt que le fruit d'une invention, il s'agit davantage de la découverte par les premiers développeurs Elm de la même structure basique au sein de leur code. C'était un peu bizarre de voir les gens se retrouver avec du code architecturé de la même manière sans en avoir discuté ensemble avant !

Évidemment, l'Architecture Elm est facile à réaliser avec Elm, mais elle peut être utile à tout projet front-end. En fait, des projets tels que Redux ont été inspirés par l'architecture Elm, et il est donc possible que vous en ayez déjà vu des dérivées ailleurs. Le fait est que, même si vous ne pouvez pas encore utiliser Elm au travail, vous tirerez beaucoup de bénéfices de l'utilisation d'Elm et de la compréhension de son modèle.

## Le modèle de base

Les programmes Elm ressemblent toujours à ça:

![Diagram of The Elm Architecture](buttons.svg)

Ce programme Elm produit du HTML afin d'être affiché à l'écran, et l'ordinateur émet ensuite en retour des messages au sujet de ce qu'il se passe. "Ils ont cliqué sur un bouton !".

Mais que ce passe-t-il au sein d'un programme Elm ? Celui-ci est toujours fait de trois parties :

  * **Model** &mdash; (le modèle) l'état de l'application
  * **View** &mdash; (la vue) une manière de transformer cet état en HTML,
  * **Update** &mdash; une manière de transformer cet état à partir des messages reçus.

Ces trois concepts sont le cœur de **L'Architecture Elm**.

Les quelques exemples qui suivent, montrent comment utiliser ce modèle pour des interactions avec l'utilisateur via des boutons et champs texte. Ils rendront cela plus concret !

## Suivez les exemples

Les exemples sont tous disponibles dans l'éditeur en ligne:

[![online editor](try.png)](https://elm-lang.org/try)

Cet éditeur montre des conseils dans le coin supérieur gauche :

<video id="hints-video" width="360" height="180" autoplay loop style="margin: 0.55em 0 1em 2em;" onclick="var v = document.getElementById('hints-video'); v.paused ? (v.play(), v.style.opacity = 1) : (v.pause(), v.style.opacity = 0.5)">
  <source src="hints.mp4" type="video/mp4">
</video>

N'hésitez pas à suivre ces conseils si vous rencontrez des difficultés !
