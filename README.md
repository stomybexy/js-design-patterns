# Modèles de conception en Javascript

*Auteur*: *Jonatan SOMBIE*

## Prototype
Le modèle **Prototype** est un modèle de conception consistant à créer un objet à partir d'un patron dont il est le clone. Cet objet *patron* est le prototype de l'objet créé. 
Ce modèle est intégré dans Javascript qui est un langage à prototypes.
En javascript on définit un prototype en créant une fonction *constructeur*. On utilise le mot clé ```this``` pour définir les propriétés dont hériteront les objets créés à partir de ce prototype.

Dans l'exemple ci-dessous, nous créons deux objets ayant la même structure sans utilisation de prototypes.

>Exemple: 
```js
var jo = {
    firstname: 'John',
    lastname: 'Travolta',
    age: 45,
    sex: 'M',
    fullname: function() {
        return this.firstname + ' ' + this.lastname;
    }
};

var ben = {
    firstname: 'Benois',
    lastname: 'Koenig',
    age: 35,
    sex: 'M',
    fullname: function() {
        return this.firstname + ' ' + this.lastname;
    }
}
```
En procédant ainsi, nous écrivons plus de lignes de codes et créons la fonctions fullname plusieurs fois. Nous pourrions bien entendu créer la fonction en dehors des objets et l'affecter aux attributs fullname. Une meilleure manière de procéder est de créer un *prototype* et d'instancier les objets en utilisant ce prototype comme présenté ci-dessous:

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

### Avantages
* L'usage des prototypes rend le code plus facile à maintenir. Dans notre exemple, si plutard nous changeons le contenu de la fonction fullname, ou si nous souhaitons ajouter une nouvelle méthode *isOld*, il nous suffirait de modifier le *prototype*.
* Il améliore la testabilité de notre code. En effet, la possibilité de modifier le comportement de tous les objets créés à partir d'un même prototype en modifiant juste le prototype permet l'usage aisé de mocks.

### Inconvénients
* Il ne permet pas l'accès à des membres privés. Dans l'exemple ci-dessous, nous utilisons dans la fonction *constructor* une variable privée *privateVar* inaccessible aux méthodes définie en dehors de la fonction.
>Exemple: 

```js
function Person() {
    var privateVar = 4;
    
    this.internalMeth = function() {
        console.log(privateVar); // This is OK  privateVar == 4
    }
}

Person.prototype.externalMeth = function() {
    console.log(privateVar); // Not OK - privateVar is undefined
}
```
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
=> [Voir dans jsfiddle](https://jsfiddle.net/jonsom/r8a7Lfw1/2/)

## Module
Ce modèle de conception permet d'exposer des méthodes et variables publiques tout en maintenant des méthodes et variables privées au sein d'un objet. Il utilise le concept de **IIFE** pour (*Immediately-Invoked Functional Expression*). A la différence de *IIFE* qui renvoie une fonction, le module est un objet renvoyé par une expression fonctionnelle immédiatement exécutée. 

> Exemple:
>Dans l'exemple ci-dessus où l'on simule une automatisation de maison, les variables ```temperature``` et ```doorOpen``` sont des *variables privées* de notre module. L'on ne peut y accéder qu'à travers les méthodes publiques exposées par le module, dans notre cas ```increaseTemp```, ```decreaseTemp```, ```openDoor``` et ```closeDoor```.
=> Voir [jsfiddle](https://jsfiddle.net/jonsom/60Lqqfnf/)

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
        },
        getStatus: function() {
           return {
                temperature: temperature,
                doorOpen: doorOpen
           };
        }
    }
})();
```


### Avantages
* La possibilité de créer des variables privées permet l'*encapsulation* de parties de code dont on souhaite sécuriser la manipulation. Ceci est particulièrement important lorsque l'on développe un *framework* ou une *api*.

### Désavantages
* Ce modèle peut rendre difficile la mise en place de tests automatisés des méthodes et variables privées.

## Singleton
En Javascript, ce modèle permet le partage de resources. Il permet d'assurer qu'une même référence d'un objet est utilisée dans plusieurs parties de notre application. 
L'exemple ci-dessous montre un exemple de *singleton*.
>Exemple:
Dans cet exemple, ```service``` et ```init``` sont privés. L'on obtient une référence à notre singleton via la méthode publique ```getService```.
=> Voir dans [jsfiddle](https://jsfiddle.net/jonsom/13vnd8kq/2/).
```js
var LoginService = (function() {
   var service;
   function init() {
        var user;
        return {
            login: function(email, password) {
                //...
            },
            logout: function() {
                //...
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
Dans l'exemple ci-dessous, nous illustrons le modèle par une case à cocher activant et désactivant trois boutons. 

> *Exemple*:
Dans cet exemple, une case à cocher, le *sujet*, déclenche l'activation et la désactivation de trois boutons selon qu'elle est cochée ou non.

=> Voir dans [jsfiddle](https://jsfiddle.net/jonsom/8np3e6ma/).
On suppose qu'on a la page ```html```:

```html
<input type="checkbox" id="check">Toggle buttons
<button id="button1">Button1</button>
<button id="button2">Button2</button>
<button id="button3">Button3</button>
```
```js
// Subject interface
function Subject() {
    this.observers = [];
}
Subject.prototype.addObserver = function(observer) {
    this.observers.push(observer);
}
Subject.prototype.removeObserver = function(observer) {
    this.observers.splice(this.observers.indexOf(observer), 1);
}
Subject.prototype.notify = function(value) {
    for(var i = 0; i < this.observers.length; i++){
        this.observers[i].update(value);
    }
}

// Utility function : extend an object with extension
function extend(obj, extension) {
    for ( var key in extension ){
     obj[key] = extension[key];
  }
}

var check = document.getElementById("check");

var button1 = document.getElementById("button1");
var button2 = document.getElementById("button2");
var button3 = document.getElementById("button3");

function update(checked) {
	this.disabled = checked;
}

extend(check, new Subject());

button1.update = update;
button2.update = update;
button3.update = update;



check.onclick = function() {
	check.notify(check.checked);
}

check.addObserver(button1);
check.addObserver(button2);
check.addObserver(button3);
```
### Avantages
* Découplage entre les objets. Dans ce modèle, l'on peut faire évoluer le *sujet* et les *observers* indépendamment tant qu'ils implémentent toujours les bonnes interfaces.
* Il rend le code testable car l'interaction entre les objets se fait à travers des interfaces bien précises.

### Inconvénients
* Le *sujet* doit maintenir une liste des *observers* et doit fournir une interface d'enregistrement et de désinscription. 
Si nous avons plusieurs sujets dans notre application, chaque sujet implémente une fonctionnalité qui est en fin de compte générique. Cela est résolu par le modèle *publish/subscribe* que nous aborderons par la suite.

## Publish/Subscribe
Ce modèle de conception est une variante du modèle *observer*. Il introduit un *médiateur* entre le *sujet* et l'*observer*. Le *sujet* publie des événements à travers le médiateur qui notifient les *observers* ayant souscrit à cet événement. Le médiateur permet également la désinscription des *observers*.

>Exemple: L'exemple ci-dessous est une implémentation basique du modèle *publish/subscribe*. => Voir dans [jsfiddle](https://jsfiddle.net/jonsom/vt2bqxLw/).


```html
<div id="alert">

</div>
```

```js
var Mediator = (function(){
	
	var channels = {}; // Les chaines de souscriptions
  var chId = 0; // id de souscription
  
  return {
  	subscribe: function(channel, callback) {
    	if(!channels[channel]) {
      	channels[channel] = [];
      }
      var id= chId++;
      channels[channel].push({
      	id: id,
        callback: callback
      });
      return id;
    },
    publish: function(channel, args){
    	if(!channels[channel]) {
      	return false;
      }
      var subscribers = channels[channel];
      for(var i=0; i< subscribers.length; i++) {
      	subscribers[i].callback(args);
      }
      return this;
    },
    unsubscribe: function(id) {
    	for(var channel in channels) {
      	if(channels[channel]) {
        	for(var i = 0; i < channels[channel].length; i++) {
          	if(channels[channel][i].id === id){
            	channels[channel].splice(i, 1);
              return id;
            }
          }
        }
      }
      return this;
    }
  }
})();

var alertHandler = function(alert){
	console.log(alert);
	document.getElementById("alert").innerHTML  = alert.message;
}

var id = Mediator.subscribe("alert", alertHandler);
console.log("Subscription id", id);

var displayAlert = function(message){

		Mediator.publish("alert", {message:message});
}

setTimeout( function() {
	displayAlert("My first alert");
}, 100); // après 100ms

setTimeout( function() {
	displayAlert("My second alert");
},1000); // après 1sec

setTimeout(function() {
	Mediator.unsubscribe(id);
}, 1200); // après 1sec200ms

setTimeout( function() {
	displayAlert("This will be ignored");
},2000); // après 2s

```

### Avantages
* Ce modèle a les mêmes avantages que le modèle *observer* avec en plus un découplage plus poussé.

### Inconvénients
* Le sujet et les *observers* étant très découplés, le sujet ne peut faire aucune action ou supposition basée sur le fait qu'il existe un *observer* pour l'événement publié. Une telle conception doit s'assurer du niveau d'indépendance nécessaire entre le *sujet* et les *observers*.


* Références * : 
* **[Learning JavaScript Design Patterns - Volume 1.7.0](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)** by *Addy Osmani* 

