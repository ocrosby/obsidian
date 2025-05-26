# JavaScript Refresher

## Where can JavaScript Run?

JavaScript code can be included in any website.

Thanks to [Node.js](https://nodejs.org/en) or [Deno](https://deno.com/) JavaScript code can be executed outside of the browser as well.

With extra technologizes like [Capacitor](https://capacitorjs.com/) or [React Native](https://reactnative.dev/) you can build mobile apps based on JavaScript.

## Adding JavaScript Code to a Website

### Between script tags

```html
<script>
alert('Hello')
</script>
```

### Via Script Import 

```html
<script src="script.js"></script>
```

### Using defer

```html
<script src="assets/scripts/app.js" defer></script>
```

This would defer the execution of the Javascript file until after the body of the document is read and parsed. If the script code needs to deal with html elements in the body of the document and begins executing immediately it may attempt to reference elements that are not available yet.

### Using module type

```html
<script src="assets/scripts/app.js" type="module"></script>
```

This unlocks the import syntax within the `app.js` file.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>JavaScript Refresher</title>
    <link rel="stylesheet" href="assets/styles/main.css" />
    <meta charset=="UTF-8" />
    <script src="assets/scripts/app.js" type="module" defer></script>
  </head>
  <body>
  ...
  </body>
</html>
```

Note: The react build process injects script tag .

	Your code is transformed behind the scenes. This happens by a translator in `react-scripts` (see the package.json fil)