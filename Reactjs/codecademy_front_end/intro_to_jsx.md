# Intro to JSX

```js
// app.js
import React from 'react';
import { createRoot } from 'react-dom/client';

const name = 'Gerdo';

const helloWorld = (
  <div className='hello'>
    <h1>Hello world</h1>
    <h2>addition { 2 + 3 }</h2>
    <h3>Hello { name }</h3>
  </div>
);

const sideLength = "200px"
const panda = (
  <img
    src="images/panda.jpg"
    alt="panda"
    height={sideLength}
    width={sideLength}/>
)

const container -= document.getElementById('app');
const root = createRoot(container);
root.render(helloWorld);
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  </head>
  <body>
    <main id="app"></main>
  </body>
</html>
```

self closing tags
