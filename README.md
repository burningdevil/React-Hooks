# React Hooks

## Overview

### Class component and function component

```js
// function component
function aa(props) {
    return <></>
}

// class component
class bb extends React.Component {
    render() {
        return <></>
    }

    componentDidMount() {

    }
}
```

### Hooks

function component is a pure function.  
Stateless, No lifecycle methods.  
Decoupled, easy to read/write/test.

**React Hooks** are the feature that React added to allow you use more of React’s features without classes.  
Hooks let you “hook into” React state and lifecycle features from function components. Hooks don’t work inside classes — they let you use React without classes.

## useState

let you add a state variable to your component.

```js
// class
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };

    // ...
}

// function
function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);
  // ...
}
```

Like state, the `set` function lets you update the state and triger a re-render.

```js
import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);

  function handleClick() {
    setCount(count + 1);
  }

  return (
    <button onClick={handleClick}>
      You pressed me {count} times
    </button>
  );
}
```

### diff in passing updater function and next state directly

the `set` functon only update the state variable for the next render. If you read the state variable after calling the set function, you will still get the old value that was on the screen before your call.

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(age + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment(); // 43
        increment(); // 43
        increment(); // 43
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

The *update function* will take the pending state and calculats the next state from it.

```js
import { useState } from 'react';

export default function Counter() {
  const [age, setAge] = useState(42);

  function increment() {
    setAge(a => a + 1);
  }

  return (
    <>
      <h1>Your age: {age}</h1>
      <button onClick={() => {
        increment(); // 43
        increment(); // 44
        increment(); // 45
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

## useRef

let you reference a value that's not needed for rendering.

```js
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
    </button>
  );
}
```

### Usage

**Do not write or read ref.current during rendering.**

```js
function MyComponent() {
  // ...
  // 🚩 Don't write a ref during rendering
  myRef.current = 123;
  // ...
  // 🚩 Don't read a ref during rendering
  return <h1>{myOtherRef.current}</h1>;
}
```

**You can read or write refs from event handlers or effects instead.**

```js
function MyComponent() {
  // ...
  useEffect(() => {
    // ✅ You can read or write refs in effects
    myRef.current = 123;
  });
  // ...
  function handleClick() {
    // ✅ You can read or write refs in event handlers
    doSomething(myOtherRef.current);
  }
  // ...
}
```

### Manipulating the Dom with ref

```js
import { useRef } from 'react';

export default function Form() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>
        Focus the input
      </button>
    </>
  );
}
```

## useEffect

You’ve likely performed data fetching, subscriptions, or manually changing the DOM from React components before. We call these operations “side effects” (or “effects” for short) because they can affect other components and can’t be done during rendering.  
The *Effect Hook* lets you perform side effects in function components.

```js
// class 
class Example extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }

  componentDidMount() {
    document.title = `You clicked ${this.state.count} times`;
  }
  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  render() {
    return (
      <div>
        <p>You clicked {this.state.count} times</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Click me
        </button>
      </div>
    );
  }
}

// function
function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### Usage

A setup function with setup code that connects to that system.
It should return a cleanup function with cleanup code that disconnects from that system.
A list of dependencies including every value from your component used inside of those functions.

```js
// function
import { useEffect } from 'react';
import { createConnection } from './chat.js';

function ChatRoom({ roomId }) { 
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
  	const connection = createConnection(serverUrl, roomId);
    connection.connect(); // setup
  	return () => {
      connection.disconnect(); // clean up
  	};
  }, [serverUrl, roomId]); // ✅ you must specify them as dependencies of your Effect
  // ...
}
```

1. *setup code* runs when your components mounted
2. After every re-render where the dependencies have changed:
    * *cleanup code* runs with old porps and state.
    * *setup code* runs with the new props and state.
3. *cleanup code* runs one final time after component unmounted.

#### Connecting to an external system

```js
import { useEffect } from 'react';
import { createConnection } from './chat.js';

function ChatRoom({ roomId }) {
  const [serverUrl, setServerUrl] = useState('https://localhost:1234');

  useEffect(() => {
  	const connection = createConnection(serverUrl, roomId);
    connection.connect();
  	return () => {
      connection.disconnect();
  	};
  }, [serverUrl, roomId]);
  // ...
}
```

#### Fetching data with Efffects

```js
import { useState, useEffect } from 'react';
import { fetchBio } from './api.js';

export default function Page() {
  const [person, setPerson] = useState('Alice');
  const [bio, setBio] = useState(null);

  useEffect(() => {
    let ignore = false;
    setBio(null);
    fetchBio(person).then(result => {
      if (!ignore) {
        setBio(result);
      }
    });
    return () => {
      ignore = true;
    };
  }, [person]);

  // ...
```

## useContext

lets you read and subscribe to context from your component.

```js
/// page.js
function MyPage() {
  return (
    <ThemeContext.Provider value="dark">
      <Form />
    </ThemeContext.Provider>
  );
}

function Form() {
  // ... renders buttons inside ...
}

/// button.js
import { useContext } from 'react';

function Button() {
  const theme = useContext(ThemeContext);
  // ...
}
```

### Usage

#### Passing data deeply into the tree

```js
import { createContext, useContext } from 'react';

const ThemeContext = createContext(null);

export default function MyApp() {
  return (
    <ThemeContext.Provider value="dark">
      <Form />
    </ThemeContext.Provider>
  )
}

function Form() {
  return (
    <Panel title="Welcome">
      <Button>Sign up</Button>
      <Button>Log in</Button>
    </Panel>
  );
}

function Panel({ title, children }) {
  const theme = useContext(ThemeContext);
  const className = 'panel-' + theme;
  return (
    <section className={className}>
      <h1>{title}</h1>
      {children}
    </section>
  )
}

function Button({ children }) {
  const theme = useContext(ThemeContext);
  const className = 'button-' + theme;
  return (
    <button className={className}>
      {children}
    </button>
  );
}

```


## useMemo and useCallback

lets you cache the result of a calculation or a function definition between re-renders.

```js
import { useMemo, useCallback } from 'react';

// memo
function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    // calculation function
    () => filterTodos(todos, tab),
    [todos, tab] // dependencies
  );
  // ...
}

// callback
export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
```

### Usage

#### Skiping expensive recalculations

```js
export default function TodoList({ todos, tab, theme }) {
  // Every time the theme changes, this will be a different array...
  const visibleTodos = filterTodos(todos, tab);
  return (
    <div className={theme}>
      {/* ... so List's props will never be the same, and it will re-render every time */}
      <List items={visibleTodos} />
    </div>
  );
}
```

```js
export default function TodoList({ todos, tab, theme }) {
  // Tell React to cache your calculation between re-renders...
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab] // ...so as long as these dependencies don't change...
  );
  return (
    <div className={theme}>
      {/* ...List will receive the same props and can skip re-rendering */}
      <List items={visibleTodos} />
    </div>
  );
}
```


## useReduer

lets you add a reducer to your component.

```js
import { useReducer } from 'react';

function reducer(state, action) {
  // ...
}

function MyComponent() {
  const [state, dispatch] = useReducer(reducer, { age: 42 });
  // ...
}
```


## Recep

### Only Call Hooks at the Top Level

**Don’t call Hooks inside loops, conditions, or nested functions.** Instead, always use Hooks at the top level of your React function, before any early returns. By following this rule, you ensure that Hooks are called in the same order each time a component renders. 

### Only Call Hooks from React Functions

**Don’t call Hooks from regular JavaScript functions**. Instead, you can:

✅ Call Hooks from React function components.  
✅ Call Hooks from custom Hooks (we’ll learn about them on the next page).

By following this rule, you ensure that all stateful logic in a component is clearly visible from its source code.
