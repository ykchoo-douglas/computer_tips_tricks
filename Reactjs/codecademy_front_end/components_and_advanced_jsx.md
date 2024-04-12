# Components and Advanced JSX

```js
// Button.js
import React from 'react';

function SubmitButton() {
  // Event Listener and Event Handlers in a Component
  const handleClick = () => {
    alert('Submission Successful.');
  };
  return <button onClick={handleClick}>Submit</button>;
}

export default SubmitButton;
```

```js
// TonighsPlan.js
import React from 'react';

const fiftyFifty = Math.random() < 0.5;

function TonightsPlan() {
  // Use a Conditional in a Function Component
  if (fiftyFifty) {
    return <h1>Tonight I'm going out WOOO</h1>;
  } else {
    return <h1>Tonight I'm going to bed WOOO</h1>;
  }
}

export default TonightsPlan;
```

```js
// Friend.js
import React from 'react';

const friends = [
  {
    title: 'Yummmmmmm',
    src: 'https://content.codecademy.com/courses/React/react_photo-monkeyweirdo.jpg',
  },
  {
    title: 'Hey Guys! Wait Up!',
    src: 'https://content.codecademy.com/courses/React/react_photo-earnestfrog.jpg',
  },
  {
    title: 'Yikes',
    src: 'https://content.codecademy.com/courses/React/react_photo-alpaca.jpg',
  },
];

function Friend() {
  // Putting Logic in a Function Component
  const friend = friends[0];
  return (
    <div>
      <h1>{friend.title}</h1>
      <img src={friend.src} alt={friend.title} />
    </div>
  );
}

export default Friend;
```

```js
// RedPanda.js
import React from 'react';

const redPanda = {
  src: 'https://upload.wikimedia.org/wikipedia/commons/b/b2/Endangered_Red_Panda.jpg',
  alt: 'Red Panda',
  width: '200px',
};

// Use a Variable Attribute in a Component
function RedPanda() {
  return (
    <div>
      <h1>Cute Red Panda</h1>
      <img src={redPanda.src} alt={redPanda.alt} width={redPanda.width} />
    </div>
  );
}

export default RedPanda;
```

```js
// App.js
import React from 'react';
import RedPanda from './RedPanda';

function App() {
  return <RedPanda />;
}

export default App;
```

```js
// index.js
import React from 'react';
import ReactDOM from 'react-dom/client';

import App from './App';

ReactDOM.createRoot(document.getElementById('app')).render(<App />);
```
