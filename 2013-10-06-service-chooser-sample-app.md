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


