# Concours de CodeGolf pour gagner une place au Devfest Nantes 2019

## L'énoncé

   Vous êtes l'assistant de Jane Skillz, la rockstar à la mode du groupe JayDeyJay.
   Jane veut faire plaisir à ses fans et faire une séance de dédicaces.
  
   Chaque fan va se présenter un à un pour obtenir son autographe.
   Jane passe 3 minutes par fan pour discuter et donner son autographe sur papier.
  
   Mais Jane a quelques excentricités:
   - Jane va offrir une photo dédicacée à tous les fans dont leur ordre d'arrivé est un nombre premier (2, 3, 5, 7, ..., 29, 31..)
   - Si un fan a plus de voyelles que de consonnes dans son nom, alors elle va passer 11 minutes avec lui
   - Si un fan possède 2 lettres identiques consécutives dans son nom, alors elle va passer 7 minutes avec lui et lui offrir une photo dédicacée

   (Note: les photos de nombre premier et lettres consécutives sont cumulables. Un fan peut donc avoir 2 photos.)
   (Note 2: Si un fan a 2 lettres identiques consécutives ET plus de voyelles que de consonnes, Jane passera 15 minutes avec lui)
  
   En tant que son assistant, vous devez calculer le temps en minutes ainsi que le nombre de photos distribuées durant la séance.
  
   #### INPUT/OUTPUT
  
   Input: Un tableau de prénoms
  
   Output: Un tableau avec: Le temps en minutes et le nombre de photos distribuées
  
   #### EXEMPLE
   
   ```javascript
   const seance = ["Johnn", "Paulo", "Ringo", "George"]
   // Johnn => 7min + 1photo
   // Paulo => 11min + 1photo
   // Ringo => 3min + 1photo
   // George => 3min
   console.log([24, 3]);
   ```
   
   #### PRIX

   Pour gagner la place au DevFest Nantes 2019, il faut réussir à faire une solution correcte en JavaScript avec le plus petit nombre de caractères possibles.
   Pour valider votre solution:
     - Envoyez le lien de votre codepen (ou équivalent) à @Errorname_ sur Twitter en DM

   Fin du concours: Dimanche 22 Septembre, 23h55

   Des questions sur le challenge ? Contactez @Errorname_ sur Twitter !

   Bonne chance ! 🤘
   
   ---

## Version finale du code

```javascript
const challenge = s => (s.map(v=>{for(j=++i;i%--j;);p+=!--j+(u=/(.)\1/i.test(v));d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]|4*u|3},d=p=i=0),[d,p])
```

## Explication du code

La premiere particularité est l'utilisation de `()` pour remplacer les `{}` autours du corps de la fonction. En JS, si une fonction retourne une expression du type `(a, b, c, d)`, alors `a`, `b`, `c` et `d` seront executés, et la derniere valeur (`d`) sera retournée.

Cela permet d'economiser les 6 lettres du mot clé `retour`.

On commence par utiliser une `map` pour passer sur l'ensemble des elements de notre tableau en entrée. On n'utilise pas la valeur de retour de la `map`, mais c'est 4 lettres de moins que `forEach`.

On pourra noter l'utilisation du [2ème argument de la map](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/map#Param%C3%A8tres). Habituellement celui-ci est utilisé pour définir la valeur du `this`. Ici on s'en sert pour initialiser nos variables. On ne gagne pas de caractères par rapport a une initialisation plus classique avant la map, c'est juste pour des points de style.

```javascript
for(j=++i;i%--j;);
```

Cette ligne a plusieurs objectifs. Premierement elle permet d'incrémenter la valeur de `i` et ainsi de connaitre l'ordre d'arriver de la personne.

Elle permet également de déterminer si `i` est premier ou non en utilisant la condition d'arret de la boucle `for`. Celle ci continue de s'exécuter jusqu'a ce que le [modulo](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Op%C3%A9rateurs/Op%C3%A9rateurs_arithm%C3%A9tiques#Reste_()) du `i%j` soit égale a 0. La décrémentation de `j` permet de tester l'ensemble des nombres en de `i-1` à `1`. On peut également noter l'importance du `;` a la fin de cette ligne pour s'assurer qu'on execute pas de code dans cette boucle.

Si le nombre est premier, alors `j` sera égale a 1 a la fin de la boucle. Si ce n'est pas le cas, alors `j` sera égale a `0` (si `i===1`), ou sera supérieur a `1`.

```javascript
p+=!--j+(u=/(.)\1/i.test(v));
```

Sur cette ligne on va déterminer le nombre de photos `p`. On peut diviser l'expression en 2.

Le premier membre incrémente le nombre de photo si l'ordre d'arrivé est premier. On doit donc tester si `j===1`. Le plus simple serait de faire un simple test `j==1` pour 4 caractères. Le soucis c'est que l'opérateur `==` à une priorité plus faible que l'opérateur `+`, ce qui veut dire que l'on serait obligé d'ajouter des `()` autours de l'expression. 

La 2ème partie utilise les regex. On commence par ajouter le [flag `i`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/RegExp#Param%C3%A8tres) a la regex pour s'assurer que la casse ne pose pas de problèmes. La regex en elle meme est assez simple. On commence par capturer tous les caractères avec `(.)`, puis on vérifie que le caractère suivant est identique en utilisant `\1` qui est égale a la valeur du premier groupe de capture `(.)`. Le fait d'utiliser [`test`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/RegExp/test) plutot que [`exec`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/RegExp/exec) nous permet également d'etre sur que le résultat est un boolean, `exec` retournant un `null` ou `array` que l'on ne peut pas additioner directement.

On en profite également pour stocker le résultat dans une variable `u` pour s'en servir plus tard.

```javascript
d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]|4*u|3
```

Il ne nous reste plus qu'a déterminer la durée `d` passée pour chaque personne. Pour cela on n'a qu'une seule valeur a déterminer, vu que l'on a deja stocker la condition des lettres identiques consécutives dans `u`.

La méthode la plus simple pour savoir si on a plus de voyelles que de consonne, c'est compter le nombre de voyelles puis comparer par rapport a la taille totale du prénom divisée par 2. En revanche c'est tres inéfficace pour nous car cela nous force a utiliser la propriété `length` 2 fois. C'est 12 lettres au total !

A la place on exploite une particularité des `string`. On peut [acceder a chaque caractère sous la forme `str[i]`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/String#Acc%C3%A9der_%C3%A0_un_caract%C3%A8re). On peut donc acceder au caractères qui se trouve a l'index `2*n-1` avec `n` le nombre de voyelle, et si celui ci est `undefined` on a plus de voyelles que de consonnes.

Il ne nous reste qu'a determiner le nombre de voyelles, ce que l'on peut faire assez simplement avec la regex `/[aeiouy]/gi` qui capture chaque voyelle individuellement dans un tableau dont on récupere la taile totale.

Finalement on aurait pu additionner les elements de cette ligne avec un simple opérateur `+`:
```javascript
d+=8*!v[v.match(/[aeiouy]/gi).length*2-1]+4*u+3
```

Mais comme les valeurs a additioner tombe tres bien d'un point de vue binaire (`0011`, `0100`, et `1000`), je me suis fait plaisir avec un `|` qui est l'[opérateur `OR` binaire](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Op%C3%A9rateurs/Op%C3%A9rateurs_binaires#(OU_binaire)). Une fois de plus c'est juste pour les points de styles.


