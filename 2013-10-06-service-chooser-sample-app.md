# Service chooser in Ember

I've found [this sample app](http://tutorialzine.com/2013/05/quick-tip-convert-backbone-to-angularjs/) in Angular.js and I thought it would be a good idea to convert it to Ember.js. It's something like ~20 lines of javascript code so perfect for newcomers willing to approach the framework!

This is what we're going to build: [JSBin](http://jsbin.com/oNUfeTe/1/edit?html,js) / [DEMO](http://jsbin.com/oNUfeTe/1)


## Static mockup
Let's start from this base state. Check out the [JSBin](http://jsbin.com/oNUfeTe/3/edit?html,js).  
There are two important things to note here.


### Ember does nothing until the application is bootstrapped.

An instance of a new Ember application is therefore assigned to the `App` global variable, which is now the namespace of all the code we'll be writing.

    App = Ember.Application.create();

### Everything is rendered inside templates.

The apparently akward `<script>` tag containing our markup is parsed by Handlebars to produce the desired output and attached to the DOM. This looks exactly like regular HTML but just because it's all static.  
Pay special attention to the template name declared as an attribute of the `<script>` tag.

    <script type="text/x-handlebars" data-template-name="index">

We're telling Ember this is the template for the `index` Route. So what's a Route? And how does the framework automagically render that template without even me telling it to do so?


### Conventions, conventions, conventions

You might have already read it somewhere: Ember is **heavily** convention oriented. Until you keep naming your stuff the right way, everything will work.  
What that means is that you don't need to explicetly declare all your objects, if Ember can't find an object to instantiate, it will create an appropriate default instance under the hood.

This is exactly what happened here, Ember required an `IndexRoute` (note the naming convention) to exists, but we didn't declared it, so it instantiated a perfectly valid Route that made the application work.

So **why index**? And again, what is the purpose of this Route object?

Ember makes you think upfront about your URLs, as the framework behaves differently depending on the URL. We won't look at this (powerful) side of the framwork now, because our simple app have just one URL `/` (the root), which is mapped to the `IndexRoute`. That's why that route has been instantiated even if we didn't specified anything.

By convention, the `IndexRoute` will look for a template named `index` to render. It is as simple as that.  
A Route has a lot of responsabilities, but for the purposes of this example let's say it **renders** the template and **provides** the data (in our case the list of services).


## List of services

Time to build something dynamic. Here's the [JSBin](http://jsbin.com/oNUfeTe/5/edit?html,js).

What we want is to define a list of services in our Route in the form of JSON objects.

    [
      { title: 'web development', price: 200 },
      { title: 'web design', price: 250 },
      { title: 'photography', price: 100 },
      { title: 'coffee drinking', price: 10 }
    ]

And display them in our previously defined template.  
A Route (in this case the `IndexRoute`, which we're going to define soon) returns the appropriate data in a method named **model**. Let's define it.

    App.IndexRoute = Ember.Route.extend({
        model: function() {
            return [
                { title: 'web development', price: 200 },
                { title: 'web design', price: 250 },
                { title: 'photography', price: 100 },
                { title: 'coffee drinking', price: 10 }
            ];
        }
    });

We're **overriding** the default `IndexRoute` implementation, that Ember was using before, and declaring our own. It will work identically as we're defining just a single method, the only difference being our template will have some data to consume now.

### Make the template dynamic

Between the `Route` and the `Template` there is a `Controller` that wires things together. In fact, a model isn't injected directly into the template but is bound to a controller which is then attached to a template. We'll cover the controller briefly later, what's important to note now is that the template *talks* directly to it.

As always, a predefined controller is instantiated for us by Ember. In this case it will inherit from a particular class (namely `ArrayController`) because **the model is an array**. This is not that interesting, except for the fact that when we reference `controller` from our template we will be able to loop over each value.

That's enough to understand what is going on here, basically we're looping over every object of the services array (which is our **model**, provided by the `IndexRoute`) and displaying title and price.


    <ul id="services">
      {{#each controller}}
        <li>
          <label>
            <input type="checkbox">
            
            {{title}}
            <span>${{price}}</span>
          </label>
        </li>
      {{/each}}
    </ul>
    
     
## Update the Total
There is a last step, which is updating the total whenever a checkbox is checked/unchecked.

We'll need to use the `{{input}}` helper in our template to make the checkbox react to the application state (and viceversa, make the application react whenever the checkbox is updated).

So change the textbox into this.

    {{input type="checkbox" checkedBinding="isChecked"}}

This looks easy to grasp, but what is that `checkedBinding` thing? We're telling Ember to **bind** the state of the checkbox to a property of the controller. It's pretty simple, basically the controller (yet to be defined) we'll have an `isChecked` property that will map directly to the checkbox.

If the checkbox is **unchecked** then `isChecked` will be **false**.  
If the checkbox is **checked** then `isChecked` will be **true**.

Similarly, if we update the `isChecked` property within our code, the checkbox will immediatly reflect the changes.

This is probably the most powerful feature we've seen so far, which is called **Two way binding**. No more manual observing shit in jQuery, just declare your bindings and everything will automatically update!


