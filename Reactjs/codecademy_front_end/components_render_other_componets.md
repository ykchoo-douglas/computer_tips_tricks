# Components Render Other Components

```js
// ProfilePage.js
import React from 'react';
import NavBar from './NavBar';

function ProfilePage() {
  return (
    <div>
      {/*Apply a Component in a Render Function*/}
      <NavBar />
      <h1>All About Me!</h1>
      <p>I like movies and blah blah blah blah blah</p>
      <img src="https://content.codecademy.com/courses/React/react_photo-monkeyselfie.jpg" />
    </div>
  );
}

export default ProfilePage;
```

```js
// NavBar.js
import React from 'react';

function NavBar() {
  const pages = [
    'home',
    'blog',
    'pics',
    'bio',
    'art',
    'shop',
    'about',
    'contact',
  ];
  const navLinks = pages.map((page) => {
    return <a href={'/' + page}>&nbsp;{page}</a>;
  });

  return <nav>{navLinks}</nav>;
}
export default NavBar;
```
