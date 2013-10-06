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


## Conventions, conventions, conventions

You might have already read it somewhere: Ember is **heavily** convention oriented. Until you keep naming your stuff the right way, everything will work.  
What that means is that you don't need to explicetly declare all your objects, if Ember can't find an object to instantiate, it will create an appropriate default instance under the hood.

This is exactly what happened here, Ember required an `IndexRoute` (note the naming convention) to exists, but we didn't declared it, so it instantiated a perfectly valid Route that made the application work.

So **why index**? And again, what is the purpose of this Route object?

Ember makes you think upfront about your URLs, as the framework behaves differently depending on the URL. We won't look at this (powerful) side of the framwork now, because our simple app have just one URL `/` (the root), which is mapped to the `IndexRoute`. That's why that route has been instantiated even if we didn't specified anything.

By convention, the `IndexRoute` will look for a template named `index` to render. It is as simple as that.  
A Route has a lot of responsabilities, but for the purposes of this example let's say it **renders** the template and **provides** the data (in our case the list of services).

