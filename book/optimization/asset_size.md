# Taille des ressources

La seule chose plus lente que de manipuler le DOM est la communication entre serveurs, en particulier pour les usagers mobiles ou avec une connexion limitée. Vous pouvez passer du temps à optimiser votre code avec `Htlm.Lazy` et `Html.Keyed`, votre application restera aussi lente que son chargement.

Celui-ci peut être améliorer grandement en transférant moins d'octets. Par exemple, si la taille d'une ressource de 122ko peut-être réduite à 9ko, alors son chargement sera accéléré! Ce genre de résultats peut être obtenu en utilisant les techniques suivantes :

- **Compilation.** Le compilateur Elm peut réaliser des optimisations comme l'élimination de code mort ou le renommage de champs. Il peut retirer le code non utilisé et raccourcir les noms de champs comme `userStatus` dans le code généré.
- **Minification.** Dans l'univers JavaScript, il existe des outils appelés "minifiers" qui réalisent des transformations diverses : ils raccourcissent des variables, génèrent du code en ligne, traduisent des instructions `if` en opérateur ternaire, ou encore convertissent `'\u0041'` en `'A'`. Tout ce qui peut économiser quelques octets!
- **Compression.** Une fois le code raccourci au maximum, il est possible d'utiliser un algorithme de compression comme gzip pour en réduire la taille. C'est particulièrement efficace pour les mots-clés tels que `function` et `return` dont il est impossible de se débarrasser dans le code lui-même.

Elm facilite la mise en place de tout ça dans votre projet, sans recours à un système de construction complexe. Il suffit de deux commandes depuis le terminal!


## Instructions

La première étape consiste à compiler avec l'option `--optimize`. C'est elle qui réalise les optimisations comme le renommage de champs.

La seconde étape est de minifier le code JavaScript produit. J’utilise `uglifyjs`, mais libre à vous d'utiliser un autre outil. L'intérêt d'`uglifyjs` réside dans ses options spéciales. Celles-ci débloquent des optimisations d'ordinaire peu fiables sur du code JavaScript standard, mais qui s'avèrent très efficace sur le code généré par Elm grâce à sa conception.

En combinant ces deux étapes, optimiser `src/Main.elm` revient à exécuter:

```bash
elm make src/Main.elm --optimize --output=elm.js
uglifyjs elm.js --compress 'pure_funcs=[F2,F3,F4,F5,F6,F7,F8,F9,A2,A3,A4,A5,A6,A7,A8,A9],pure_getters,keep_fargs=false,unsafe_comps,unsafe' | uglifyjs --mangle --output elm.min.js
```

En sortie, vous obtiendrez un fichier `elm.js` et plus petit fichier `elm.min.js`!

> **Note 1:** `uglifyjs` est exécuté deux fois ici. D'abord avec l'option `--compress`, puis avec l'option `--mangle`. Sans cela, `uglifyjs` ignorera l'option `pure_funcs`.
>
> **Note 2:** Si `uglifyjs` n'est pas disponible dans votre terminal, il est possible de l'installer avec `npm install uglify-js --global`. Si `npm` n'est pas non plus disponible, obtenez le avec [nodejs](https://nodejs.org/).


## Scripts

Étant donné qu'il est difficile de se rappeler toutes les options à utiliser pour `uglifyjs`, nous vous recommandons d'écrire un script.

Dans l'hypothèse où nous voulons un script Bash qui produise les fichiers `elm.js` et `elm.min.js`, nous pouvons définir `optimize.sh` pour MacOS ou Linux comme ci-dessous :

```bash
#!/bin/sh

set -e

js="elm.js"
min="elm.min.js"

elm make --optimize --output=$js "$@"

uglifyjs $js --compress 'pure_funcs=[F2,F3,F4,F5,F6,F7,F8,F9,A2,A3,A4,A5,A6,A7,A8,A9],pure_getters,keep_fargs=false,unsafe_comps,unsafe' | uglifyjs --mangle --output $min

echo "Compiled size:$(wc $js -c) bytes  ($js)"
echo "Minified size:$(wc $min -c) bytes  ($min)"
echo "Gzipped size: $(gzip $min -c | wc -c) bytes"
```

En lançant `./optimize.sh src/Main.elm` sur l'application [TodoMVC](https://github.com/evancz/elm-todomvc), j'obtiens le résultat suivant :

```
Compiled size:  122297 bytes  (elm.js)
Minified size:   24123 bytes  (elm.min.js)
Gzipped size:     9148 bytes
```

Plutôt correct ! Seulement 9ko à transférer à l'utilisateur.

Les commandes importantes sont ici `elm` et `uglifyjs`, qui sont toutes deux disponibles sur toutes les plateformes. Il ne devrait donc pas y avoir de difficultés à faire fonctionner ce script sous Windows.


## Conseils

Il est recommandé d'écrire une `Browser.application` et de la compiler en un seul fichier JavaScript comme détaillé précédemment. Celui-ci sera téléchargé (et mis en cache) dès la première visite. Elm génère des fichiers plutôt compacts par rapport à ses concurrents, comme présenté [ici](https://elm-lang.org/blog/small-assets-without-the-headache). Cette stratégie peut donc vous mener loin.

> **Note:** En théorie, il serait possible d'obtenir des ressources encore plus compacts avec Elm. Ce n'est pas possible pour le moment, mais, si vous travaillez sur des bases de code Elm supérieures à 50k lignes, nous serions intéressés par vos retours d'expérience. Pour plus d'informations, c'est par [ici](https://gist.github.com/evancz/fc6ff4995395a1643155593a182e2de7)!
