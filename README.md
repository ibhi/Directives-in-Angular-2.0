# Directives-in-Angular-2.0
# A sneak peek in to Directives in Angular 2.0

## Disclaimer
* Angular 2.0 is still in alpha state (as of 26-May-2015) and is evolving at a very fast rate with breaking changes between most of the releases. So it is not suitable for production application and I would recommend Angular 1.x for such applications. 
* Also I am not an expert in Angular 2.0 and I am not a part of Angular team. I am just another guy trying to learn and trying to share my learnings. 
* The examples in the below article are written using angular 2.0.0-alpha.25 and I will try to keep the below code samples up to date if possible. 

## Introduction to Angular 2.0
"Angular is a development platform for building mobile and desktop web applications. Angular includes a wealth of essential features such as mobile gestures, animations, filtering, routing, data binding, security, internationalization, and beautiful UI components. It's extremely modular, lightweight, and easy to learn." - angular.io

If you want to know more about Angular 2.0, visit the official http://angular.io and for learning resources refer [this](https://github.com/timjacobi/angular2-education) which is a collection of links to various learning resources by timjacobi. 

## Introduction to Directives in Angular 2.0
There are lot of tutorials on the Internet about creating **Components** (`Components` are basically a sub class of `Directives` and in other words Components are Directives with Views) in angular 2, however there are very less tutorials or explanations about **Directives** in angular 2 and the below is my attempt to fill the void. My article below is heavily influenced by the API docs in angular.io site. Also most or all of the things discussed here will also applicable for `Components`.

And as this article is focused on Directives, I assume you have basic understanding of the Angular 2.0 and its glossary of terms like, Components, Annotations(Decorators), DI, Change Detection etc.

### What is a Directive?
<!-- A directive can be an existing DOM element or an attribute or an custom angular element, whose default behavior is altered. -->
Directives allows you to attach behavior to elements in the DOM. It is that simple!. I will try to explain the various properties of Directives using some example codes.

#### Example 1 (selector)

Let us have a look at a simple example to understand this better. Let us create an simple directive called 'red', which attached to any DOM element as an attribute, will change the color of its contents to red. Here is the code to create the directive.

```javascript
@Directive({
  selector: '[red]',
})

class RedDec {
  constructor(el: ElementRef) {
    el.domElement.style.color = 'red';
  }
}
```

A directive consists of a single directive annotation and a controller class. `@Directive` is the annotation used to define a directive in angular 2.  When the directive's `selector` matches elements in the DOM angular instantiates directives for each matched element. In the controller class `RedDec`, we define the behavior of the directive. Here we are using the elements `style.color` property to set the color to red.
Now in your HTML code you can use the `red` directive like below and the `Hello World` text will be rendered in red color.

```html
<h1 red> Hello World</h1>
````

Here you may have multiple questions like why the `selector` is using `[]` and what the hell is this `el:ElementRef`? Let us go one by one. First `selector` uses CSS selector approach to match the elements in the DOM.

> [Here](http://plnkr.co/edit/dFShDzOAU3TdLQbKYcbc?p=preview) is the plnkr link to the full working code for the above example.

Angular only allows directives to trigger on CSS selectors that do not cross element boundaries.

`selector` may be declared as one of the following:

```javascript
element-name: select by element name.
.class: select by class name.
[attribute]: select by attribute name.
[attribute=value]: select by attribute name and value.
:not(sub_selector): select only if the element does not match the sub_selector.
selector1, selector2: select if either selector1 or selector2 matches.
```
Now I hope you would have got the answer to the first question. As our directive is basically an attribute, we have used `[red]` for the `selector`. We can also combine some of the above to further refine the selection matching for example we can use `input[type=text]` to select only the input elements of type text.

I will try to answer the second question at a later stage, as we have to dwell little bit deep in to dependency injection. But for now we are trying to get an reference to the DOM element by dependency injection.

Alright, now we have a basic understanding of the Angular 2.0 Directives and we go a bit further and try to expand on the above example.

#### Example 2 (properties)

So in the previous example if we add the `red` attribute to any DOM element, the contents of the element color is displayed in red. What if we want to add an `color` attribute to an element and change the elements color based on the value supplied to the attribute.

```javascript
@Directive({
  selector: '[color]',
  properties: {
    'colour' : 'color'
  }
})

class ColorDec {
  el: ElementRef;
  
  constructor(el: ElementRef) {
    this.el = el;
  }
  
  set colour(colour){
    this.el.domElement.style.color = colour;
  }
}
```

Here we have a similar `selector` which selects the elements with `color` attribute on them, additionally we have a `properties` property which defines a set of directiveProperty to bindingProperty key-value pairs:

* directiveProperty specifies the component property where the value is written.
* bindingProperty specifies the DOM property where the value is read from.

In our example above `colour` (not `color`) is the directive property which binds to and reads its value from `color` bindings property on the DOM element. Whenever the `color` attribute value changes  in the DOM, it triggers the `set colour` method on the directive and the `colour` property is set to the new value.

> [Here](http://plnkr.co/edit/kNuXAQEPie1HNXQeGrZe?p=preview) is the plnkr link to the full working code for the above example.

