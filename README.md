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

## Module
Ce modèle de conception permet d'exposer des méthodes et variables publics tout en maintenant des méthodes et variables privées au sein d'un objet. Il utilise le concept de **IIFE** pour (*Immediately-Invoked Functional Expression*). A la différence de *IIFE* qui renvoie une fonction, le module est un objet renvoyé par une expression fonctionnelle immédiatement exécutée. 
> Exemple:
```js
var ModuleMaison = (function(){
    var temperature = 20;
    var porteOuverte = false;
    return {
        augmenterLaTemperature: function() {
            temperature++;
            return temperature;
        },
        baisserLaTemperature: function() {
            temperature--;
            return temperature;
        },
        ouvrirLaPorte: function() {
            porteOuverte = true;
            return true;
        },
        fermerLaPorte: function() {
            porteOuverte = false;
            return false;
        }
    }
})();
```
>Dans l'exemple ci-dessus où l'on simule une automatisation de maison, les variables ```temperature``` et ```porteOuverte``` sont des *variables privés* de notre module. L'on ne peut y accéder qu'aux travers des méthodes publiques exposées par le module, dans notre cas ```augmenterLaTemperature```, ```baisserLaTemperature```, ```ouvrirLaPorte``` et ```fermerLaPorte```.

### Avantages
* La possibilité de créer des variables privées permet l'*encapsulation* de parties de code dont on souhaite sécuriser la manipulation. Ceci est particulièrement important lorsque l'on développe un *framework* ou une *api*.

### Désavantages
* Ce modèle peut rendre difficile la mise en place de tests automatisés des méthodes et variables privées

## Singleton
En Javascript, ce modèle permet le partage de resources. Il permet d'assurer qu'une même référence d'un objet est utilisée dans plusieurs parties de notre application. 
>Exemple:
```js
var LoginService = (function() {
   var service;
   function init() {
        var utilisateur;
        return {
            login: function(email, motDePasse) {
                ...
            },
            logout: function() {
                ...
                utilisateur = null;
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
