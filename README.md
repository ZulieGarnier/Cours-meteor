# Cours-meteor

#Meteor

Nous allons découvrir Meteor qui est un framework JavaScript qui permet de faire des Web Apps ou encore des applications mobiles hybrides.

Nous allons prendre le code final du tutoriel qui est proposé sur le site officiel de meteor https://www.meteor.com/tutorials/blaze/creating-an-app

Ce tutoriel s'adresse à des personnes qui ont déjà bien pris en main JavaScript.

Tout d'abord vous devez installer meteor

`curl https://install.meteor.com/ | sh`

Ensuite allez dans votre terminal et placez vous là ou vous souhaitez créer votre projet meteor, ensuite exécutez la commande suivante :

`meteor create simple-todos`

Cette ligne de commande va générer des fichiers, si vous vous rendez dans votre projet simple-todos vous y verrez :

simple-todos.js
simple-todos.html
simple-todos.css
.meteor

Nous ne toucherons pas au dossier .meteor mais simplement au trois fichiers à la racine de notre application.

Vous pouvez déjà lancer votre application en tappant :

`meteor`

Si nous ouvrons une fenetre de navigateur nous verrons qu'il y a déjà du code mais ce n'est pas exceptionnel nous allons donc vider les fichiers HTML et JavaScript.

Commencons par le code CSS, je n'expliquerais pas le code CSS car ce n'est pas l'objet de ce cours.

###simple-todos.css

```css
/* CSS declarations go here */ 
body {
	font-family: sans-serif;
	background-color: #315481;
	background-image: linear-gradient(to bottom, #315481, #918e82 100%);
	background-attachment: fixed;
	position: absolute;
	top: 0; bottom: 0; left: 0; right: 0;
	padding: 0;
	margin: 0;
	font-size: 14px;
}
.container {
	max-width: 600px;
	margin: 0 auto;
	min-height: 100%;
	background: white;
}
header {
	background: #d2edf4;
	background-image: linear-gradient(to bottom, #d0edf5, #e1e5f0 100%);
	padding: 20px 15px 15px 15px;
	position: relative;
}
#login-buttons { display: block; }
h1 { font-size: 1.5em; margin: 0; margin-bottom: 10px; display: inline-block; margin-right: 1em; } form {
	margin-top: 10px;
	margin-bottom: -10px;
	position: relative;
}
.new-task input {
	box-sizing: border-box;
	padding: 10px 0;
	background: transparent;
	border: none;
	width: 100%;
	padding-right: 80px;
	font-size: 1em;
}
.new-task input:focus{ outline: 0; }
ul {
	margin: 0;
	padding: 0;
	background: white;
}
.delete {
	float: right;
	font-weight: bold;
	background: none;
	font-size: 1em;
	border: none;
	position: relative;
}
li {
	position: relative;
	list-style: none;
	padding: 15px;
	border-bottom: #eee solid 1px;
}
li .text { margin-left: 10px; }
li.checked { color: #888; }
li.checked .text { text-decoration: line-through; }
li.private { background: #eee; border-color: #ddd; }
header .hide-completed { float: right; }
.toggle-private { margin-left: 5px; }
@media (max-width: 600px) {
	li {
		padding: 12px 15px;
	}
	.search {
		width: 150px;
		clear: both;
	}
	.new-task input {
		padding-bottom: 5px;
	}
}
```

##Le fichier JavaScript :

La première ligne créée une nouvelle collection (on peut considérer une collection comme une table dans une base de données).

```javascript
Tasks = new Mongo.Collection("tasks");
```

Nous définissons un helpers (un helpers est une fonction que l'on pourra éxécuter depuis de HTML) « tasks » qui va retourner tous les éléments de notre collection. 

```javascript
Template.body.helpers({
    tasks: function() {
      return Tasks.find({}, {sort: {createdAt: -1}});
    }
  });
```

Ensuite nous allons ''écouter'' sur l'élément submit (le bouton) qui à la classe « .new-task » grâce au paramètre 'event'.
Nous allons pouvoir récupérer la valeur de l'élément puis nous inséront notre élément dans la collection « tasks » grâce à Tasks.insert({}) ;

```javascript
  Template.body.events({
    "submit .new-task": function(event) {
      event.preventDefault();
      // Récupère la valeur de l'élément du formulaire
      var text = event.target.text.value;

      // Insert une tâche dans notre collection
      Tasks.insert({
        text: text,
        createdAt: new Date()
      });
      // La ligne suivante s'occupe de vider l'élément submit
      event.target.text.value = "";
    }
  });
```

Nous déclarons ensuite deux fonctions.
La première marque la tâche comme faite ou non en fonction de sa valeur précédente.

```javascript
    "click .toggle-checked": function() {
      // Définit la propriété « checked » à l'opposé de sa valeur actuelle
      Tasks.update(this._id, {
        $set: {checked: ! this.checked}
      });
    }
```

La seconde fonction supprime l'élément.

```javascript
  "click .delete": function() {
    Tasks.remove(this._id);
  }
```

Nous pouvons aussi entrer du code qui sera exécuté uniquement sur le serveur, mais nous n'allons pas en avoir besoin ici.

```javascript
if (Meteor.isServer) {
  Meteor.startup(function () {
    // L'exécution de code sur le serveur au démarrage
  });
}
```

###simple-todos.js

```javascript
Tasks = new Mongo.Collection("tasks");

if (Meteor.isClient) {
  Template.body.helpers({
    tasks: function() {
      return Tasks.find({}, {sort: {createdAt: -1}});
    }
  });

  Template.body.events({
    "submit .new-task": function(event) {
      event.preventDefault();
      // Récupère la valeur de l'élément du formulaire
      var text = event.target.text.value;

      // Insert une tâche dans notre collection
      Tasks.insert({
        text: text,
        createdAt: new Date()
      });
      // La ligne suivante s'occupe de vider l'élément submit
      event.target.text.value = "";
    }
  });

  Template.task.events({
    "click .toggle-checked": function() {
      // Définit la propriété « checked » à l'opposé de sa valeur actuelle
      Tasks.update(this._id, {
        $set: {checked: ! this.checked}
      });
    },
    "click .delete": function() {
      Tasks.remove(this._id);
    }
  });
}

if (Meteor.isServer) {
  Meteor.startup(function () {
    // L'exécution de code sur le serveur au démarrage
  });
}
```

##Le code HTML :

Nous allons appeler le helpers « tasks », que nous avons vu dans le code JavaScript ce qui fait que nous inserons le template (on détaillera juste en dessous ce qu'est un template) « task » autant de fois qu'il y a d'éléments dans notre collection.

```html
{{#each tasks}}
	{{> task}}
{{/each}}
```

Ensuite nous définissons le template (un template est un morceau de code HTML que l'on inclus autant de fois que l'on veut) qui va nous permettre d'afficher les informations sur les tâches.

```html
<template name="task">
  <!-- La ligne suivante s'occupe d'ajouter ou d'enlever la classe « checked » -->
  <li class="{{#if checked}}checked{{/if}}">
    <button class="delete">&times;</button>
    <input type="checkbox" checked="{{checked}}" class="toggle-checked" />
    <span class="text">{{text}}</span>
  </li>
</template>
```

A la ligne 27 nous voyons « {{text}} » qui récupère la valeur de la propriété text de l'élément de notre collection.

```html
<span class="text">{{text}}</span>
```

###simple-todos.html

```html
<head>
  <title>my-todo</title>
</head>

<body>
  <div class="container">
    <header>
      <h1>Todo List</h1>

      <form class="new-task">
        <input type="text" name="text" placeholder="Type to add new tasks" />
      </form>
    </header>

    <ul>
      {{#each tasks}}
        {{> task}}
      {{/each}}
    </ul>
  </div>
</body>

<template name="task">
  <li class="{{#if checked}}checked{{/if}}">
    <button class="delete">&times;</button>
    <input type="checkbox" checked="{{checked}}" class="toggle-checked" />
    <span class="text">{{text}}</span>
  </li>
</template>
```

Nous voila arriver à la fin de ce cours si votre application n'est pas lancée, vous pouvez la lancer grâce à cette commande :

meteor

Et y accédé via votre navigateur à l'adresse localhost:3000

J'espère que ce cours vous a plu, si vous souhaitez appronfondir le sujet il existe un tutoriel plus complet dont presque la totalité à été traduit en français.

http://fr.discovermeteor.com/chapters/getting-started/

Bon courage. :)
