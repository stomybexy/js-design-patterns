# Modèles de conception en Javascript

## Prototype
Le modèle **Prototype** est un modèle de conception consistant à créer un objet à partir d'un patron dont il est le clone. Cet objet *patron* est le prototype de l'objet créé. 
Ce modèle est intégré dans Javascript qui est un langage à prototypes.
En javascript on définit un prototype en créant une fonction *constructeur*. On utilise le mot clé ```this``` pour définir les propriétés dont hériteront les objets créés à partir de ce prototype.

>Exemple: 
```js
function Person(firstname, lastname, age, sex) {
    this.firstname = firstname;
    this.lastname = lastname;
    this.age = age;
    this.sex = sex;
}
Person.prototype.fullname = function() {
    return this.firstname + ' ' + this.lastname;
}
var jo = new Person('John', 'Travolta', 45, 'M');
var ben = new Person('Benois', 'Koenig', 35, 'M');
```
>```Person.prototype``` est le protype des objets ```jo``` et ```ben```. ```Person``` est la fonction ```constructeur```.
>En définissant la *méthode*  ```fullname```en dehors de la fonction constructeur, on s'assure qu'elle est partagée entre toutes les instances créées à partir de ce prototype et qu'elle n'est donc pas redéfinie à chaque instanciation d'objet.
Cette méthode de définition des *méthodes* est à préférer à celle ci-dessous:
```js
function PersonBad(firstname, lastname, age, sex) {
    this.firstname = firstname;
    this.lastname = lastname;
    this.age = age;
    this.sex = sex;
    this.fullname = function() {
        return this.firstname + ' ' + this.lastname;
    }
}
```
Chaque objet créé avec le constructeur ```PersonBad``` aura sa propre copie de la méthode ```fullname``` qui est recréée à  chaque appel à  ```new PersonBad(...)```.
## IIFE (Immediately-Invoked Functional Expression)
Cette technique permet de capturer l'état d'une variable à un moment donné et de l'utiliser dans la définition d'une fonction de sorte que le fonctionnement de la fonction ne soit pas affecté par des modifications ultérieures de la variable.
>Exemple:
* Without *IIFE*
```js
for(var i=0; i<10; i++){
        setTimeout(function() {
            console.log(i);
        }, 0);
}
```
>Ce code affichera uniquement ```10```, la dernière valeur de ```i```. En effet, du fait de ```setTimeout``` l'exécution de la boucle ```for``` précède celle de ```console.log(i)```. Ainsi lorsque le code ```console.log(i)``` s'éxécute`,```i``` a la valeur ```10```. Pour remédier à cela et afficher ```0 ... 9``` de façon *asynchrone* (après la boucle for), l'on peut utiliser la construction suivante:
* With IIFE
```js
for(var i=0; i<10; i++){
    (function(val) {
        setTimeout(function() {
            console.log(val);
        }, 0);
    })(i);
}
```
Cette technique permet de capturer la valeur de ```i``` dans la boucle et d'atteindre le résultat attendu. 
De façon générale, un IIFE se présente de la sorte:

L'on construit une fonction que l'on exécute immédiatement en lui passant en paramètre la valeur à capturer. Le paramètre de cette fonction est alors utilisé pour construire une nouvelle fonction dont l'éxécution n'est pas altérée par les modifications ultérieures de la variable passée en paramètre de la fonction englobante.
>Exemple:
```js
var toCapture = 'Original value';
var myFunction = (function(value){
    return function() {
        // L'on utilise value 
        console.log(value); 
    }
})(toCapture);
toCapture = 'Modified';
myFunction(); // Affiche Original value
```

## Module
Ce modèle de conception permet d'exposer des méthodes et variables publiques tout en maintenant des méthodes et variables privées au sein d'un objet. Il utilise le concept de **IIFE** pour (*Immediately-Invoked Functional Expression*). A la différence de *IIFE* qui renvoie une fonction, le module est un objet renvoyé par une expression fonctionnelle immédiatement exécutée. 
> Exemple:
```js
var Home = (function(){
    var temperature = 20;
    var doorOpen = false;
    return {
        increaseTemp: function() {
            temperature++;
            return temperature;
        },
        decreaseTemp: function() {
            temperature--;
            return temperature;
        },
        openDoor: function() {
            doorOpen = true;
            return true;
        },
        closeDoor: function() {
            doorOpen = false;
            return false;
        }
    }
})();
```
>Dans l'exemple ci-dessus où l'on simule une automatisation de maison, les variables ```temperature``` et ```doorOpen``` sont des *variables privées* de notre module. L'on ne peut y accéder qu'à travers les méthodes publiques exposées par le module, dans notre cas ```increaseTemp```, ```decreaseTemp```, ```openDoor``` et ```closeDoor```.

### Avantages
* La possibilité de créer des variables privées permet l'*encapsulation* de parties de code dont on souhaite sécuriser la manipulation. Ceci est particulièrement important lorsque l'on développe un *framework* ou une *api*.

### Désavantages
* Ce modèle peut rendre difficile la mise en place de tests automatisés des méthodes et variables privées.

## Singleton
En Javascript, ce modèle permet le partage de resources. Il permet d'assurer qu'une même référence d'un objet est utilisée dans plusieurs parties de notre application. 
>Exemple:
```js
var LoginService = (function() {
   var service;
   function init() {
        var user;
        return {
            login: function(email, password) {
                ...
            },
            logout: function() {
                ...
                user = null;
            }
        }
   }
   return {
        getService: function() {
            if(!service) {
                service = init();
            }
            return service;
        }
   }
})();
var serviceA = LoginService.getService();
var serviceB = LoginService.getService();
console.log(serviceA === serviceB); // affiche true
```
> A noter que ```LoginService``` est un module.

## Observer
C'est un modèle de conception où un objet (le *subjet*) maintient une liste d'objets dépendants (les *observers*) qu'il notifie en cas de changement de son état.
L'on peut inscrire et désinscrire un objet (*observer*) de ces notifications.
