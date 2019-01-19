# Caffeinated Style Sheets

Extend `.css` with JavaScript the easy way!

![](https://i.imgur.com/b7DUBle.png)

Below is information, learning resources, code, and demos relating to extending CSS in valid `.css` files using JavaScript

## How Event-Driven Styling Works

1. You listen to global events in the browser, or events on DOM nodes, or observers: ResizeObserver, IntersectionObserver, and MutationObserver
2. When an event you're listening to is triggered, you call a "stylesheet function" that returns CSS. This can be a string of CSS, or a [CSSStylesheet](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet) object, or a [CSSRule](https://developer.mozilla.org/en-US/docs/Web/API/CSSRule) object, or other ways of representing a set of property declarations for one or more elements
3. After the stylesheet function is processed, the returned CSS is applied to the document, or output as objects or a string

[![](https://i.imgur.com/GwgUWLi.png)](https://twitter.com/innovati/status/1083400451243065354)

Using this simple model we can polyfill existing CSS features in old browsers using small amounts of CSS and JavaScript that run very fast, as well as prototype and implement new styling functionality that's not part of CSS.

- you never have to go lower-level than CSS (don't use JS to reinvent CSS, use it to extend CSS)
- do everything in CSS that you can do in CSS
- use JavaScript to make CSS higher-level and _more_ declarative
- split your styles by the _events_ that drive their calculation

> Both the `jsincss` and `computed-variables` plugins linked below are event-driven style managers and can be used to extend CSS stylesheets (and rules), and CSS variables

## Writing Caffeinated Stylesheets

You can use the event-driven model for processing style information with plugins like [`jsincss`](https://www.npmjs.com/package/jsincss) and [`computed-variables`](https://www.npmjs.com/package/computed-variables) from JavaScript, and you can build on top of them with your own plugins and your own abstractions for storing and processing style information.

The idea of Caffeinated Style Sheets, or writing CSS that's extended by JavaScript builds on top of what these plugins provide and are concerned with how to store JS-powered style information in _valid_ CSS syntax, as well as easy ways to read that information out of CSSOM and DOM so JavaScript can work with it, avoiding the need for custom syntaxes or having to express everything in JavaScript.

This is all about keeping your CSS in CSS and your Javascript in JavaScript, but finding ways to write CSS and JavaScript that compliment each other and make advanced styling techniques effortless.

### Writing Custom at-rules in CSS

You can invent custom at-rules in valid CSS syntax by extending [`@supports () {}`](https://drafts.csswg.org/css-conditional-3/#at-supports) using a custom identifier like `--custom()` or `--example()`, and if you have arguments to pass into the function to encode them as something [`JSON.parse()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) can read to maximum compatibility and simplicity. This is useful for polyfilling at-rules coming to CSS like `@document`, as well as inventing your own queries, like element queries or container queries.

```css
@supports (--custom("arg1", 2, {"arg": 3})) {
  div {
    color: blue;
  }
}
```

This is valid CSS that includes information that's simple for JavaScript to find, parse, and process, we could end up with a call to a JavaScript function like this:

```js
custom("arg1", 2, {"arg": 3}, `
  div {
    color: blue;
  }
`)
```

### Writing Custom selectors in CSS

CSS syntax also includes the ability for us to invent any attribute selector we want, so if we invent a custom attriute selector like [`[--custom]`](https://drafts.csswg.org/selectors-4/#attribute-selectors) we can be sure that will never match anything in HTML. If we have arguments to pass into the function we can encode them in a format `JSON.parse()` and read and extract that with JavaScript later. In this example it wouldn't be hard to read this and extract `custom("div", "arg1", 2, {"arg": 3})` to run with a JavaScript function named `custom()`:

```css
div[--custom='"arg1", 2, {"arg": 3}'] {
  color: blue;
}
```

## Writing Custom Properties in CSS

CSS already includes the ability for us to define [custom properties](https://drafts.csswg.org/css-variables/) as long as we start the name with a double-dash `--` to ensure it never collides with any future CSS property. What's great about custom properties (aka css variables) in CSS is that they can be defined and set in CSSOM (css stylesheets) or in DOM (html `style=""` attribute), and can be read and written from JavaScript. This makes them an amazing way for JavaScript functions to supply values to CSS that it can use for styling:

In CSS:

```css
div {
  --custom: ["arg1", 2, {"arg": 3}];
  color: var(--custom);
}
```

or in HTML: 

```html
<div style='--custom: ["arg1", 2, {"arg": 3}];'></div>
```

### Slides to the “Caffeinated Style Sheets” talk

- [Slides to “Caffeinated Style Sheets”](https://tomhodgins.com/caffeinated-style-sheets.pdf) given at [FITC WebUnleashed 2018](https://fitc.ca/presentation/caffeinated-style-sheets-extending-css-with-javascript/)

## Demos

- [deqaf-demo](https://github.com/tomhodgins/deqaf-demo)
- [qaffeine-demo](https://github.com/tomhodgins/qaffeine-demo)
- [`#merryCSSmas` plugin roundup with deqaf](https://gist.github.com/tomhodgins/c22973a0990248b244cd56c7641b31f0)
- [`#merryCSSmas` plugin roundup with qaffeine](https://gist.github.com/tomhodgins/f483eff2a5345b9a890388eb4cbe296a)
- [Caffeinated Style Sheets collection on Codepen](https://codepen.io/collection/DNjKgR/)
- [Computed Variables collection on Codepen](https://codepen.io/collection/DJNaar/)

## Plugins

### Event-Driven Style Managers (CSS reprocessors)

- [jsincss](https://github.com/tomhodgins/jsincss) (manage dynamic stylesheets)
- [computed-variables](https://github.com/tomhodgins/computed-variables) (update CSS variable values)

### Parse JS-powered Styles from CSS

- [deqaf](https://github.com/tomhodgins/deqaf) (parse CSSOM client-side)
- [qaffeine](https://github.com/tomhodgins/qaffeine) (parse `.css` files server-side)

### Plugins compatible with jsincss, deqaf, and qaffeine

### Custom at-rule plugins

- [jsincss-viewport](https://github.com/tomhodgins/jsincss-viewport)
- [jsincss-overflow](https://github.com/tomhodgins/jsincss-overflow)
- [jsincss-protocol-sniffer](https://github.com/tomhodgins/jsincss-protocol-sniffer)
- [jsincss-days](https://github.com/tomhodgins/jsincss-days)
- [element queries](https://github.com/tomhodgins/jsincss-element-query)
- [@document](https://codepen.io/tomhodgins/pen/ePQMpe)
- [navigator queries](https://codepen.io/tomhodgins/pen/bOgOdJ)
- [`localStorage` and `sessionStorage` queries](https://codepen.io/tomhodgins/pen/XoMYwy)
- [date queries](https://codepen.io/tomhodgins/pen/yGMqgR)

### Custom selector plugins

- [jsincss-parent-selector](https://github.com/tomhodgins/jsincss-parent-selector)
- [jsincss-has-selector](https://github.com/tomhodgins/jsincss-has-selector)
- [jsincss-closest-selector](https://github.com/tomhodgins/jsincss-closest-selector)
- [jsincss-first-selector](https://github.com/tomhodgins/jsincss-first-selector)
- [jsincss-last-selector](https://github.com/tomhodgins/jsincss-last-selector)
- [jsincss-elder-selector](https://github.com/tomhodgins/jsincss-elder-selector)
- [jsincss-previous-selector](https://github.com/tomhodgins/jsincss-previous-selector)
- [jsincss-string-match](https://github.com/tomhodgins/jsincss-string-match)
- [jsincss-regex-match](https://github.com/tomhodgins/jsincss-regex-match)
- [jsincss-custom-specificity](https://github.com/tomhodgins/jsincss-custom-specificity)
- [computed style selector](https://codepen.io/tomhodgins/pen/wYqjzY)
- [nth-letter and nth-word](https://codepen.io/tomhodgins/pen/YJZyPr)
- [playing, paused, muted, and current-time pseudo-classes for `<video>` and `<audio>`](https://codepen.io/tomhodgins/pen/aRWqro)
- [not-blank valid/invalid selector](https://codepen.io/tomhodgins/pen/JevNPb)
- [compare attribute values as numbers](https://codepen.io/tomhodgins/pen/EGKJqx)

### Utilities for building plugins

- [parse-css-stylesheet](https://github.com/tomhodgins/parse-css-stylesheet)
- [parse-css-rule](https://github.com/tomhodgins/parse-css-rule)
- [stringify-css-stylesheet](https://github.com/tomhodgins/stringify-css-stylesheet)
- [stringify-css-rule](https://github.com/tomhodgins/stringify-css-rule)
- [css-polyfill-patterns](https://github.com/tomhodgins/css-polyfill-patterns)
- [element-query-tests](https://github.com/tomhodgins/element-query-tests)
- [element-unit-tests](https://github.com/tomhodgins/element-unit-tests)

## Videos

- [Excerpt from “Caffeinated Style Sheets” talk about writing custom JS-powered selectors and custom at-rules in valid CSS syntax](https://www.youtube.com/watch?v=kGGMQvxB3WI)
- [Methods of Extending CSS Selectors and At-Rules with JavaScript ](https://www.youtube.com/watch?v=ai6dIdVeGic)
- [Defining Custom At-Rules in CSS (extending CSS with JavaScript)](https://www.youtube.com/watch?v=CJsnUC72iII)
- [An Approach for Defining Custom Events for JS-powered Rules Inside CSS Stylesheets](https://www.youtube.com/watch?v=ZExsj1OdSK4)
- [Using JS-Powered Custom Pseudo Classes from CSS](https://www.youtube.com/watch?v=-4vdpxgftW4)

### Explorations and Experiments

- [Building your own CSS Preprocessor](https://www.youtube.com/watch?v=WEkgIiwduFs)
- [Live Stream: CSS Variable Experiments](https://www.youtube.com/watch?v=UwJfbLcsYmk)
- [Live Stream: Extending CSS with JavaScript (Using Qaffeine to parse extended CSS)](https://www.youtube.com/watch?v=6pRRB1gXgPo)