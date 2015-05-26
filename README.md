# Directives-in-Angular-2.0
# A sneak peek in to Directives in angular 2.0

## Disclaimer
Angular 2.0 is still in alpha state (as of 26-May-2015) and is changing in a very fast rate with breaking changes between most of the releases. So it is not suitable for production application. 

## Introduction to Angular 2.0
"Angular is a development platform for building mobile and desktop web applications. Angular includes a wealth of essential features such as mobile gestures, animations, filtering, routing, data binding, security, internationalization, and beautiful UI components. It's extremely modular, lightweight, and easy to learn." - angular.io

## Introduction to Directives in Angular 2.0
There are lot of tutorials on the Internet about creating **Components** in angular 2, however there are very less tutorials or explanations about **Directives** in angular 2 and the below is my attempt to fill the void. My article below is heavily influenced by the API docs in angular.io site.

### What is a Directive?
<!-- A directive can be an existing DOM element or an attribute or an custom angular element, whose default behavior is altered. -->
Directives allows you to attach behavior to elements in the DOM. 
Let us have a look at a simple example to understand this better. Let us create an simple directive called 'red', which attached to any DOM element, will change the color of its contents to red. Here is the code

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