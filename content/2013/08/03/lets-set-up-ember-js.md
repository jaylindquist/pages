+++
menu = "main"
date = "Sun, 04 Aug 2013 04:12:36 +0000"
title = "Lets set up Ember.js"
draft = false
+++

<a title="Ember.js home page" href="http://emberjs.com/">Ember.js</a> is an interesting creature. This is the first client-side MVC I've looked into. Apparently calling these applications "single page apps" are looked down upon, even though they are driven from a single HTML page. Single DOM applications seems more appropriate.

Based on previous experience learning new technologies, I've found that I am only able to understand the technology once I have a model of what's going on behind the scenes. It was only when I realized how reflection and Java Beans could be used to implement Spring's inversion of control that I could really start working with it.

Before this understanding occurs, everything is magic. I don't like programming that runs on magic as magic is rather unpredictable.

With Ember.js, I struggled to follow how the examples and samples pulled everything together, but then I read the <a title="Ember.js Naming Conventions" href="http://emberjs.com/guides/concepts/naming-conventions/">Naming Conventions</a> document, and everything became clear.

To be honest, the Ember guides are quite good. They take a conversational approach to documentation instead of a fact based approach.

However, all the examples available so far have tried too hard to make the example "pretty" or "realistic". That is, the examples have plenty of HTML structure to support CSS styles and the data model pulls from a related REST service with Ember Data. This detracts from the learning process. Yes, the result looks pretty, and the data is dynamic, but the example code is cluttered and involves concepts outside of Ember.js itself.

## The Ugly App

So, here's my example, an ugly Ember.js app. It does very little: When we visit the app, a list of game franchises are listed; clicking on a franchise will list games in the franchise; and clicking on a game will show us the original release date.

This is supposed to be simple and will help show off different parts of Ember such as routes and how they connect the model to the template.

Lets get the annoying stuff out the way: This example uses Ember.js 1.0 RC6. It consists of an HTML page, a javascript file representing the application, and all dependencies. You can find the code on GitHub here: https://github.com/jaylindquist/ember-template

Since we aren't using any styles, out base HTML page is a bit bare, but we still need it:

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<title>Ember Template</title>
</head>
<body>

<!-- ember templates go here -->

<script src="js/libs/jquery-1.9.1.js"></script>
<script src="js/libs/handlebars-1.0.0-rc.4.js"></script>
<script src="js/libs/ember-1.0.0-rc.6.js"></script>
<script src="js/libs/bootstrap.min.js"></script>
<script src="js/games.js"></script>

</body>
</html>
```

All the Ember heavy lifting takes place in `js/games.js`.

## Ember Parts

In order to get Ember up and running, we need some common components:

* Application - the main Ember application
* Model - our actual data with methods to get at the information
* Router - a description of the paths supported by the application and how the app will determine which models to load
* Routes - how to tell ember which models to load based on the current URL / route
* Templates - how to actually display the information to the user

### Application
We start with the Application. This is easy enough:

```
App = Ember.Application.create({});
```

### Model

I'll say little about the model for now. In this case, I am not using Ember Data. Instead, I am using a plain old JavaScript object. I then store the object in the application for easy reference.

All you really need to know is that the methods on the model exist and do what they say:

```
Store = {
    franchises: Ember.A(),
    defaultFranchise: function() {...},
    get: function(id) {...},
    init: function() {
        if(this.franchises.length == 0) {
            this.franchises.pushObject(App.Franchise.create({
                id: 0,
                title:'Final Fantasy',
                games: Ember.A(),
                defaultGame: function() {...},
                get: function(id) {...},
                init: function() {...}
            }));
            this.franchises.pushObject(App.Franchise.create({...}));
            ...
        });
    }
};

App.Franchise = Ember.Object.extend({});
App.Game = Ember.Object.extend({});

// assign the model to the App for reference
App.GameStore = Store;
App.GameStore.init();
```

### Router
The router will define most of our application. When creating a router, you define the routes with names and paths. Once you have a name of a route, Ember will automatically look for routes and controllers with specific names. So, a route named `franchise` will have a `FranchiseRoute` and a `FranchiseController`. See the naming conventions for more information.

```
App.Router.map(function() {
    this.resource('franchise', { path:'/franchise/:franchise_id'}, function() {
        this.resource('game', { path: '/game/:game_id' });
    });
});
```

Here, we've defined two routes, a `franchise` route that Ember will load whenever `/franchise` is part of the path. An optional franchise ID can also be a part of the path. The second route is under the `franchise` route. Whenever Ember sees the `/franchise/{franchise_id}/game` URL, the `game` route will be loaded.

### Routes

Even though we have only defined two routes, there are four routes that we want to define.

* `ApplicationRoute (/)` - What happens when the root of the site (`/`) is visited? The `ApplicationRoute` is used to add a list of franchises to the controller.
* `IndexRoute (/)` - If the `ApplicationRoute` is only adding a list of franchises, what is getting displayed? Simple, the root of the site will actually be directed to the `IndexRoute`, which, in this case, will redirect the user to the franchise route with the default franchise.
* `FranchiseRoute (/franchise/{franchise_id})` - Now that we've reached the franchise route, we will need to load the model. In this case, we want to take the `franchise_id` that we defined in the `App.Router` from the URL and load the specified franchise.
* `GameRoute (/franchise/{franchise_id}/game/{game_id})` - like the `FranchiseRoute`, this route will pull the `game_id` from the URL and load the requested game model.

Translating this to code, we get:

```
App.ApplicationRoute = Ember.Route.extend({
    setupController: function(applicationController) {
        applicationController.set('franchises', App.GameStore.franchises);
    }
});

App.IndexRoute = Ember.Route.extend({
    redirect: function() {
        this.transitionTo('franchise', App.GameStore.defaultFranchise());
    }
});

App.FranchiseRoute = Ember.Route.extend({
    model: function(params) {
        return App.GameStore.get(parseInt(params.franchise_id));
    }
});

App.GameRoute = Ember.Route.extend({
    model: function(params) {
        return this.modelFor('franchise').get(parseInt(params.game_id));
    }
});
```

Note: a url with both a franchise and a game will pass through the `ApplicationRoute`, `FranchiseRoute`, and `GameRoute`. This is important to understand because each route's template will also be rendered.

### Templates

Back to the HTML, notice above that there is a spot for us to store our templates.

This application has three templates that correspond to the four routes we created earlier:

* Default template - this template has no ID attribute, so Ember assumes it is for the base application route (`/`). This URL will trigger both the `ApplicationRoute` and the `IndexRoute`. The template will then have access to the `franchises` property we added above.
* `franchise` - this corresponds to the `FranchiseRoute` above and will be rendered as the default template's `{{outlet}}`. It will have access to the `franchise` loaded as the template's model implicitly.
* `game` - similarly, this template is loaded as the franchise's `{{outlet}}` and will have the implicit `game` model.

```
<script type="text/x-handlebars">
    <h1>Franchises</h1>
    <ul>
        {{#each franchise in franchises}}
            * {{#linkTo 'franchise' franchise}}{{franchise.title}}{{/linkTo}}
        {{/each}}
    </ul>

    {{outlet}}
</script>

<script type="text/x-handlebars" id="franchise">
    ## {{title}}
    <ul>
        {{#each game in games}}
        * {{#linkTo 'game' game}}{{game.title}}{{/linkTo}}
        {{/each}}
    </ul>
    {{outlet}}
</script>

<script type="text/x-handlebars" id="game">
    <dl>
        <dt>{{title}}</dt>
        <dd>{{releaseDate}}</dd>
    </dl>
</script>
```

Again, note that a URL with both a franchise and a game will need to render all three templates as each template will be loaded as an `{{outlet}}` in each of the parent templates.

Also of note, the templates are each loaded in the order of URL precedence. So, when Ember sees `/franchise/2/game/1` it will purposely load the default template with the franchise template in the `{{outlet}}` and the game template will be loaded in the franchise's `{{outlet}}`.

This, of course, is on purpose and is part of a RESTful architecture and design. It also helps tie everything back together. The URL, routes, and templates are (as well as controllers, not covered here) are all linked tightly. This is where Ember's magic takes place and is the first step towards understanding the framework.
