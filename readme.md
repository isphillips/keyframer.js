# keyframer.js

A unopinionated, framework-agnostic CSS-in-JS library with additional support for moving parts.


## Scoped Stylesheets

```js
import { Keyframer } from 'where-the-js-at'; 

// Instances allow for style scoping!
// All examples assume the following 'css' variable exists.
const css = new Keyframer(
	name, 	/* Unique stylesheet ID. 
				If set to "*", stylesheet is global. */
	object 	/* OPTIONAL: object to use for initial styles. */
);
```

### Add a Rule
```js
// Create a class:
css.addRule(".nice-header", {
	color: "red",
	background: "blue"
});
```

### Inherit From Other Rule(s)
```js
// Create an element rule:
css.addRule("h3", {
	...css.getRule(".nice-header") // ES6 magic ! *.* !
	boxShadow: "2px 2px black"
});
```

### Pseudo-Selectors
```js
// Create an ID:
css.addRule("#title", {
	":before": {
		// CSS strings require an internal pair of quotes.
		content: "'Start Title!!!'",
		color: "purple"
	}
});
```

### Purge Stylesheet
This function removes all rules scoped to this stylesheet:
```js
css.purge();
```

## Integrate stylesheets with HTML & JSX

CSS is no good without markup for it to style, right? Let's learn how to scope styles with **Keyframer**.

We'll use the following stylesheets for example:
```js
// Scoped stylesheet:
const scopedCss = new Stylesheet("scoped-sheet", {
	".scoped-class": { color: "red", boxShadow: "1px 1px black" }
});

// Global stylesheet:
const globalCss = new Stylesheet("*", {
	".global-class": { color: "blue", background: "red" }
});
``` 

### HTML

**Global styles** require no extra effort whatsoever, just use them normally!
```html
<h1 class="global-class">Hey!</h1>
```

**Scoped styles** are a bit different, but not very complicated. The `kf` attribute specifies which stylesheet the DOM element inherits styles from (in addition to global styles).
```html
<h1 kf="scoped-sheet" class="scoped-class">Hello!</h1>
```

### JSX

**Global styles** are easy as cake. No different than regular procedures.
```jsx
const globalH1 = <h1 className="global-class">Hey!</h1>;
```

**Scoped styles** are different, but easy once you get the hang of it. There are two ways to go about it.

1. If you are only going to use the rule a few times, then the method is same as with HTML:
```jsx
const scopedH1 = (
	<h1 className="scoped-class" kf="scoped-sheet">Hey!</h1>
);
```
2. If you predict heavy reliance on the rule(s), then do something like the following. You can access all the scoped classes as if they were global!
```jsx
const Scoped = {
	h1: (p, c) => <h1 {...p} kf="scoped-sheet">{c}</h1>
}; 

const scopedH1 = <Scoped.h1 className="scoped-class">Hello!</h1>;
```

## Working with `@keyframes` & CSS transitions

**Keyframer** has special support/syntax for `@keyframes` animations and CSS transitions, after all, it's in the name. Animations are always global, and added directly via the `Keyframer` object, rather than an instance of it.

### Creating an animation

```js
const coolZoom = Keyframe.addKeyframes("cool-zoom", {
	0: {
		transform: "none",
		color: "red"
	},
	50: {
		transform: "scale(2)"
		color: "blue"
	},
	100: {
		transform: "none"
	}
});
```

### Creating a transition

```js
const fadeZoomOut = keyframer.transition({
	duration: "1s",
	easing: "ease",

	opacity: 0,
	transform: "scale(0)"
});
```

### Animating Elements

```js
Keyframer.animate(
	"#title", coolZoom("1s", "ease", "infinite")
	() => alert("This runs when the animation is complete!")
);
```

### Animating Elements on `<Event>`

The event is a DOM event, rather than a pseudoselector (`:hover`, etc). This offers more flexibility to CSS animations.
```js
Keyframer.animateOn(
	"#title", "mouseover", fadeZoomOut()
);
```

### Animating events on `<KeyEvent>`

The `onKey` function only accepts `"up", "down", and "press"`. This function allows animations to run which certain keys are pressed/released.
```js
Keyframer.animateOnKey(
	"#title", "down", ["KeyA", "KeyD"],
	coolZoom("1s", "ease", "infinite"
);
```

## Examples

### Cool Utility Class Generator (lovely ES6)

This generates color and background-color classes.	
```js
const utils = (name, ...values) => {
	const classes = Object.create(null);
	values.forEach(value => {
		const className = `${name}-${value}`;
		classes[className] = { [name]: value } 
	});
	return classes;
};

const utilityClasses = {
	// Color Utility Classes:
	...utils("color", "red", "blue", "green", "orange"),
	// Background Utility Classes:
	...utils("background", "red", "blue", "green", "orange")
};

const styleSheet = // Use an ID of "*" to scope globally.
	Keyframer.fromObject("*", utilityClasses);
```

## License

This project is MIT-licensed! Do whatever you want. I don't care. I would love credit though.

