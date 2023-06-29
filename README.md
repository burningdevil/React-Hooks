# React Hooks


## useState

let you add a state variable to your component. 

```js
const [state, setState] = useState(initialState);
```

Like state, the `set` function lets you update the state and triger a re-render.

```js
const [age, setAge] = useState(1);

function handleClick() {
  setAge(age + 1)
  setAge(a => a + 1);
}
```

### Caveats

Cause the `set` functon only update the state variable for the next render.

1. If you read the state variable after calling the set function, you will still get the old value that was on the screen before your call.
2. 


### diff in passing updater function and next state directly

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
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

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
        increment();
        increment();
        increment();
      }}>+3</button>
      <button onClick={() => {
        increment();
      }}>+1</button>
    </>
  );
}
```

## useRef


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

Updating data passed via context




## useEffect

lets you synchronize a component with an external system.





## useMemo


## useRef
