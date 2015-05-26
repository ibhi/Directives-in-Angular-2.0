# Directives-in-Angular-2.0
# A sneak peek in to Directives in Angular 2.0

## Disclaimer
Angular 2.0 is still in alpha state (as of 26-May-2015) and is changing in a very fast rate with breaking changes between most of the releases. So it is not suitable for production application. 
The examples in the below article are written using angular 2.0.0-alpha.25 and I will try to keep the below code samples upto date if possible.

## Introduction to Angular 2.0
"Angular is a development platform for building mobile and desktop web applications. Angular includes a wealth of essential features such as mobile gestures, animations, filtering, routing, data binding, security, internationalization, and beautiful UI components. It's extremely modular, lightweight, and easy to learn." - angular.io

## Introduction to Directives in Angular 2.0
There are lot of tutorials on the Internet about creating **Components** in angular 2, however there are very less tutorials or explanations about **Directives** in angular 2 and the below is my attempt to fill the void. My article below is heavily influenced by the API docs in angular.io site.
And as this article is focused on Directives, I assume you have basic understanding of the Angular 2.0 and its glossary of terms like, component, annotations(decorators), DI etc.

### What is a Directive?
<!-- A directive can be an existing DOM element or an attribute or an custom angular element, whose default behavior is altered. -->
Directives allows you to attach behavior to elements in the DOM. 
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
Now in your HTML code you can use the `red` directive like below and the `Hello World` text will be displayed in red color.

```html
<h1 red> Hello World</h1>
````

Here you may have multiple questions like why the `selector` is using `[]` and what the hell is this `el:ElementRef`? Let us go one by one. First `selector` uses CSS selector approach to match the elements in the DOM.

> This is the plnkr link to the full working code for the above example --> http://plnkr.co/edit/dFShDzOAU3TdLQbKYcbc?p=preview

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

I will try to answer the second question at a later stage, as we have to dwell little bit deep in to dependency injection.
