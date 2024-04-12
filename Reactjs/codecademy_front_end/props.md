# Props

```js
// Button.js
import React from 'react';

// OR function Button({text = 'Default Text of Big Button'})
function Button(props) {
  // OR const {text = 'Default Text of Big Button'} = props;

  return <button>{props.text}</button>;
}
// Giving Default Values to props
Button.defaultProps = {
  text: 'Default Text of Big Button',
};

export default Button;
```

```js
// Appp.js (Button.js)
import React from 'react';
import Button from './Button.js';

function App() {
  return <Button text="" />;
}

export default App;
```

```js
// List.js
import React from 'react';

function List(props) {
  let titleText = `Favorite ${props.type}`;
  if (props.children instanceof Array) {
    titleText += 's';
  }
  return (
    <div>
      <h1>{titleText}</h1>
      <ul>{props.children}</ul>
    </div>
  );
}

export default List;
```

```js
// App.js (List.js)
import React from 'react';
import List from './List';

// Every component’s props object has a property named children.
// props.children will return everything in between a component’s opening and closing JSX tags.
// example <MyFunctionComponent> </MyFunctionComponent>
function App() {
  return (
    <div>
      <List type="Living Musician">
        <li>Sachiko M</li>
        <li>Harvey Sid Fisher</li>
      </List>
      <List type="Living Cat Musician">
        <li>Nora the Piano Cat</li>
        <li>Meow the Guitar Cat</li>
      </List>
    </div>
  );
}

export default App;
```

```js
// Talker.js
import React from 'react';
import Button from './Button';

function Talker() {
  function handleClick() {
    let speech = '';
    for (let i = 0; i < 10000; i++) {
      speech += 'blah ';
    }
    alert(speech);
  }
  // Pass an Event Handler as a prop
  return <Button onClick={handleClick} />;
}

export default Talker;
```

```js
// Button.js
import React from 'react';

function Button(props) {
  // Receive an Event Handler as a prop
  return <button onClick={props.onClick}>Click me!</button>;
}

export default Button;
```

```js
// Greeting.js
import React from 'react';

function Greeting(props) {
  // Render Different UI Based on props
  if (props.signedIn == false) {
    return <h1>Please login.</h1>;
  } else {
    return (
      <>
        <h1>Welcome back, {props.name}!</h1>
        <article>Latest Movie: A Computer Bug's Life</article>
      </>
    );
  }
}

export default Greeting;
```

```js
// App.js (Greeting.js)
import React from 'react';
import Greeting from './Greeting';

function App() {
  return (
    <div>
      <h1>MovieFlix</h1>
      <Greeting name="Alison" signedIn={true} />
    </div>
  );
}

export default App;
```

```js
// Product.js
import React from 'react';

// Render a Component's props
function Product(props) {
  return (
    <div>
      <h1>{props.name}</h1>
      <h2>{props.price}</h2>
      <h3>{props.rating}</h3>
    </div>
  );
}

export default Product;
```

```js
// App.js (Product.js)
import React from 'react';
import Product from './Product';

function App() {
  return <Product name="Apple Watch" price={999} rating="4.5/5.0" />;
}

export default App;
```

```js
// PropsDisplayer.js
import React from 'react';

function PropsDisplayer(props) {
  const stringProps = JSON.stringify(props); // return empty object {} if no passing any attribute
  return (
    <div>
      <h1>CHECK OUT MY PROPS OBJECT</h1>
      <h2>{stringProps}</h2>
    </div>
  );
}

export default PropsDisplayer;
```

```js
// App.js
import React from 'react';
import ReactDOM from 'react-dom';

import PropsDisplayer from './PropsDisplayer';

// Pass `props` to a Component
function App() {
  return <PropsDisplayer myProp="Hello" />;
}

export default App;
```
