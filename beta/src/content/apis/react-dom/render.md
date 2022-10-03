---
title: render
---

<Gotcha>

In React 18, `render` was replaced by [`createRoot`.](/apis/react-dom/client/createRoot) Using `render` in React 18 will warn that your app will behave as if it’s running React 17. Learn more [here.](https://reactjs.org/blog/2022/03/08/react-18-upgrade-guide.html#updates-to-client-rendering-apis)

</Gotcha>


<Intro>

`render` renders a piece of [JSX](/learn/writing-markup-with-jsx) ("React node") into a browser DOM node.

```js
render(reactNode, domNode, callback?)
```

</Intro>

<InlineToc />

---

## Usage {/*usage*/}

Call `render` to display a <CodeStep step={1}>React component</CodeStep> inside a <CodeStep step={2}>browser DOM node</CodeStep>.

```js [[1, 4, "<App />"], [2, 4, "document.getElementById('root')"]]
import {render} from 'react-dom';
import App from './App.js';

render(<App />, document.getElementById('root'));
````

### Rendering the root component {/*rendering-the-root-component*/}

In apps fully built with React, **you will usually only do this once at startup**--to render the "root" component.

<Sandpack>

```js index.js active
import './styles.css';
import {render} from 'react-dom';
import App from './App.js';

render(<App />, document.getElementById('root'));
```

```js App.js
export default function App() {
  return <h1>Hello, world!</h1>;
}
```

</Sandpack>

Usually you shouldn't need to call `render` again or to call it in more places. From this point on, React will be managing the DOM of your application. If you want to update the UI, your components can do this by [using state.](/apis/react/useState)

---

### Rendering multiple roots {/*rendering-multiple-roots*/}

If your page [isn't fully built with React](/learn/add-react-to-a-website), call `render` for each top-level piece of UI managed by React.

<Sandpack>

```html public/index.html
<nav id="navigation"></nav>
<main>
  <p>This paragraph is not rendered by React (open index.html to verify).</p>
  <section id="comments"></section>
</main>
```

```js index.js active
import './styles.css';
import { render } from 'react-dom';
import { Comments, Navigation } from './Components.js';

render(
  <Navigation />,
  document.getElementById('navigation')
);

render(
  <Comments />,
  document.getElementById('comments')
);
```

```js Components.js
export function Navigation() {
  return (
    <ul>
      <NavLink href="/">Home</NavLink>
      <NavLink href="/about">About</NavLink>
    </ul>
  );
}

function NavLink({ href, children }) {
  return (
    <li>
      <a href={href}>{children}</a>
    </li>
  );
}

export function Comments() {
  return (
    <>
      <h2>Comments</h2>
      <Comment text="Hello!" author="Sophie" />
      <Comment text="How are you?" author="Sunil" />
    </>
  );
}

function Comment({ text, author }) {
  return (
    <p>{text} — <i>{author}</i></p>
  );
}
```

```css
nav ul { padding: 0; margin: 0; }
nav ul li { display: inline-block; margin-right: 20px; }
```

</Sandpack>

You can destroy the rendered trees with [`unmountComponentAtNode()`.](/apis/react-dom/unmountComponentAtNode)

---

### Updating the rendered tree {/*updating-the-rendered-tree*/}

You can call `render` more than once on the same DOM node. As long as the component tree structure matches up with what was previously rendered, React will [preserve the state.](/learn/preserving-and-resetting-state) Notice how you can type in the input, which means that the updates from repeated `render` calls every second in this example are not destructive:

<Sandpack>

```js index.js active
import {render} from 'react-dom';
import './styles.css';
import App from './App.js';

let i = 0;
setInterval(() => {
  render(
    <App counter={i} />,
    document.getElementById('root')
  );
  i++;
}, 1000);
```

```js App.js
export default function App({counter}) {
  return (
    <>
      <h1>Hello, world! {counter}</h1>
      <input placeholder="Type something here" />
    </>
  );
}
```

</Sandpack>

It is uncommon to call `render` multiple times. Usually, you'll [update state](/apis/react/useState) inside one of the components instead.

---

## Reference {/*reference*/}

### `render(reactNode, domNode, callback?)` {/*render*/}

Call `render` to display a React component inside a browser DOM element.

```js
const domNode = document.getElementById('root');
render(<App />, domNode);
```

React will display `<App />` in the `domNode`, and take over managing the DOM inside it.

An app fully built with React will usually only have one `render` call with its root component.  A page that uses "sprinkles" of React for parts of the page may have as many `render` calls as needed.

[See examples above.](#usage)

#### Parameters {/*parameters*/}

* `reactNode`: A *React node* that you want to display. This will usually be a piece of JSX like `<App />`, but you can also pass a React element constructed with [`createElement()`](/apis/react/createElement), a string, a number, `null`, or `undefined`. 

* `domNode`: A [DOM element.](https://developer.mozilla.org/en-US/docs/Web/API/Element) React will display the `reactNode` you pass inside this DOM element. From this moment, React will manage the DOM inside the `domNode` and update it when your React tree changes.

* **optional** `callback`: A function. If passed, React will call it after your component is placed into the DOM.


#### Returns {/*returns*/}

`render` usually returns `null`. However, if the `reactNode` you pass is a *class component*, then it will return an instance of that component.

#### Caveats {/*caveats*/}

* In React 18, `render` was replaced by [`createRoot`.](/apis/react-dom/client/createRoot) Please use for React 18 and beyond.

* The first time you call `render`, React will clear all the existing HTML content inside the `domNode` before rendering the React component into it. If your `domNode` contains HTML generated by React on the server or during the build, use [`hydrate()`](/apis/react-dom/hydrate) instead, which attaches the event handlers to the existing HTML.

* If you call `render` on the same `domNode` more than once, React will update the DOM as necessary to reflect the latest JSX you passed. React will decide which parts of the DOM can be reused and which need to be recreated by ["matching it up"](/learn/preserving-and-resetting-state) with the previously rendered tree. Calling `render` on the same `domNode` again is similar to calling the [`set` function](/apis/react/useState#setstate) on the root component: React avoids unnecessary DOM updates.

* If your app is fully built with React, you'll likely have only one `render` call in your app. (If you use a framework, it might do this call for you.) When you want to render a piece of JSX in a different part of the DOM tree that isn't a child of your component (for example, a modal or a tooltip), use [`createPortal`](/apis/react-dom/createPortal) instead of `render`.

---