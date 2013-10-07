# Service chooser in Ember

I've found [this sample app](http://tutorialzine.com/2013/05/quick-tip-convert-backbone-to-angularjs/) in Angular.js and I thought it would be a good idea to convert it to Ember.js. It's something like ~20 lines of javascript code so perfect for newcomers willing to approach the framework!

This is what we're going to build: [JSBin](http://jsbin.com/oNUfeTe/1/edit?html,js) / [DEMO](http://jsbin.com/oNUfeTe/1)


## Static mockup
Let's start from this base state. Check out the [JSBin](http://jsbin.com/oNUfeTe/3/edit?html,js).  
There are two important things to note here.


### Ember does nothing until the application is bootstrapped.

We create a new instance of an Ember application and assign it to the global `App` variable, which is now the namespace of all the code we'll be writing.

    App = Ember.Application.create();

### Everything is rendered inside templates.

The apparently akward `<script>` tag containing our markup is parsed by Handlebars to produce the desired output and attached to the DOM. This looks exactly like regular HTML, there's no handlebars stuff yet, it's all static.  
Pay special attention to the template name declared as an attribute of the `<script>` tag.

    <script type="text/x-handlebars" data-template-name="index">

We're telling Ember this is the template for the `index` Route. So what's a Route? And how does the framework automagically render that template without even me telling it to do so?


### Conventions, conventions, conventions

You might have already read it somewhere: Ember is **heavily** convention oriented. Until you keep naming things the right way, everything will work.  
What that means is that you don't need to explicetly declare all your objects, if Ember can't find an object to instantiate, it will create an appropriate default instance under the hood.

This is exactly what happened here, Ember required an `IndexRoute` (note the naming convention) to exists, but we didn't declare it, so it instantiated a perfectly valid Route that made the application work.

So **why index**? And again, what is the purpose of this Route object?

Ember makes you think upfront about your URLs, as the framework behaves differently depending on the URL. We won't look at this (powerful) side of the framwork now, because our simple app has just one URL `/` (the root), which is mapped to the `IndexRoute`. That's why that route has been instantiated even if we didn't specify anything.

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

We're **implementing** `IndexRoute` now, so the default implementation that Ember was using before is no longer needed. It will work identically because we're defining just a single method, the only difference being our template will have some data to consume now.

### Make the template dynamic

Between the `Route` and the `Template` there is a `Controller` that wires things together. In fact, a model isn't injected directly into the template but is bound to a controller which is then attached to a template. We'll cover the controller briefly later, what's important to note here is that the template is connected to the controller (not the route).

As always, a predefined controller is instantiated for us by Ember. In this case it will inherit from a particular class (namely `ArrayController`) because **the model is an array**. This is not that interesting, except for the fact that when we reference `model` from our template we will be able to loop over each value.

That's enough to understand what is going on here, basically we're looping over every object of the services array (which is our **model**, provided by the `IndexRoute`) and displaying title and price.


    <ul id="services">
      {{#each model}}
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

So replace the checkbox with this.

    {{input type="checkbox" checkedBinding="isChecked"}}

This looks easy to grasp, but what is that `checkedBinding` thing? We're telling Ember to **bind** the state of the checkbox to a property of the controller. It's pretty simple, basically the controller (yet to be defined) we'll have an `isChecked` property that will map directly to the checkbox.

If the checkbox is **unchecked** then `isChecked` will be **false**.  
If the checkbox is **checked** then `isChecked` will be **true**.

Similarly, if we update the `isChecked` property within our code, the checkbox will immediatly reflect the changes.

This is probably the most powerful feature we've seen so far, which is called **Two way binding**. No more manual observing shit in jQuery, just declare your bindings and everything will automatically update!


### Introducing Controllers

We need to define our controller as we did before for the route (how will it be named? You guessed it, `IndexController`).

A controller basically **holds data**, which can be consumed by the template. The `IndexRoute` fetches the model (in our case it's static, but in a real app it probably would have been a call to a webservice or something like that) which is then set, by default, as the `model` property on the `IndexController`.

The other responsability of the controller is to **respond to events**. In our case, we want it to update the state of the application whenever a checkbox is checked or unchecked. Not all events belong to the controller, if a controller doesn't respond to an event it will be dispatched to the route.

So why do we need to define a controller? Because we want to **update the total** of the order when the `isChecked` property changes (and, therefore, when any of the checkboxes is updated). To do this, we define a method `total` and make it a property with the `.property()` shortcut provided by Ember (that is, it will be accessible in the same way as `title` and `price` within the template).

    App.IndexController = Ember.ArrayController.extend({
        total: function() {
            return Math.random();
        }.property('@each.isChecked')
    });

`@each.isChecked` means that the property should be re computed (the method will be called again) whenever **any** of the objects in the model will update its `isChecked` property.

This won't actually do anything because `total` is not referenced in the template. This is easily fixed.

    <p id="total">total: <span>${{total}}</span></p>

If you click around for a while, you'll notice that the total is obviously wrong, but nevertheless it is updated at the right time. What's missing is a proper sum of the prices.

The `ArrayController` has an handy method `filterBy` which filters objects by a specified property. We want those with `isChecked == true`.

    var checkedObjects = this.filterBy('isChecked', true);
    // [ { title: 'web development', price: 200 }, { title: 'photography', price: 100 } ]
    
From there, it's easy to calculate the exact total and return it.

      var checkedObjects = this.filterBy('isChecked', true),
          total = 0;
        
      checkedObjects.forEach(function(obj) {
        total += obj.price;
      });
      
      return total;

Or in a more functional way.

    return this.filterBy('isChecked', true)
			.map(function(obj) { return obj.price; })
			.reduce(function(acc, item) { return acc + item; }, 0);



## Closing words

This has taken longer than I thougt to write, hopefully you are now interested enough in the framework to go build something by yourself!
