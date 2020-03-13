# react-state-management

## :page_facing_up: 목차
* [simple count](#simple-count)
* [lift state](#simple-count)
* [context](#context)
* [context with logic](#context-with-logic)
* [context with reducer](#context-with-reducer)

## simple count

```jsx
import React from 'react'

function Counter() {
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return <button onClick={increment}>{count}</button>
}

function App() {
  return <Counter />
}

export default App
```

## lift state
```jsx
import React from 'react'

function Counter({count, onIncrementClick}) {
  return <button onClick={onIncrementClick}>{count}</button>
}

function CountDisplay({count}) {
  return <div>The current counter count is {count}</div>
}

function App() {
  const [count, setCount] = React.useState(0)
  const increment = () => setCount(c => c + 1)
  return (
    <div>
      <CountDisplay count={count} />
      <Counter count={count} onIncrementClick={increment} />
    </div>
  )
}

export default App
```

## context
```jsx
import React from 'react'

// src/count/count-context.js
const CountContext = React.createContext()

function useCount() {
  const context = React.useContext(CountContext)
  if (!context) {
    throw new Error(`useCount must be used within a CountProvider`)
  }
  return context
}

function CountProvider(props) {
  const [count, setCount] = React.useState(0)
  const value = React.useMemo(() => [count, setCount], [count])
  return <CountContext.Provider value={value} {...props} />
}

// export {CountProvider, useCount}

////////////////

// src/count/page.js

// import {CountProvider, useCount} from './count-context'

function Counter() {
  const [count, setCount] = useCount()
  const increment = () => setCount(c => c + 1)
  return <button onClick={increment}>{count}</button>
}

function CountDisplay() {
  const [count] = useCount()
  return <div>The current counter count is {count}</div>
}

function CountPage() {
  return (
    <div>
      <CountProvider>
        <CountDisplay />
        <Counter />
      </CountProvider>
    </div>
  )
}

export default CountPage
```

## context with logic
```jsx
import React from 'react'

// src/count/count-context.js
const CountContext = React.createContext()

function useCount() {
  const context = React.useContext(CountContext)
  if (!context) {
    throw new Error(`useCount must be used within a CountProvider`)
  }
  const [count, setCount] = context

  const increment = () => setCount(c => c + 1)
  return {
    count,
    setCount,
    increment,
  }
}

function CountProvider(props) {
  const [count, setCount] = React.useState(0)
  const value = React.useMemo(() => [count, setCount], [count])
  return <CountContext.Provider value={value} {...props} />
}

// export {CountProvider, useCount}

////////////////

// src/count/page.js

// import {CountProvider, useCount} from './count-context'

function Counter() {
  const {count, increment} = useCount()
  return <button onClick={increment}>{count}</button>
}

function CountDisplay() {
  const {count} = useCount()
  return <div>The current counter count is {count}</div>
}

function CountPage() {
  return (
    <div>
      <CountProvider>
        <CountDisplay />
        <Counter />
      </CountProvider>
    </div>
  )
}

export default CountPage
```

## context with reducer 
```jsx
import React from 'react'

// src/count/count-context.js

const CountContext = React.createContext()

function countReducer(state, action) {
  switch (action.type) {
    case 'INCREMENT': {
      return {count: state.count + 1}
    }
    default: {
      throw new Error(`Unsupported action type: ${action.type}`)
    }
  }
}

function CountProvider(props) {
  const [state, dispatch] = React.useReducer(countReducer, {count: 0})
  const value = React.useMemo(() => [state, dispatch], [state])
  return <CountContext.Provider value={value} {...props} />
}

function useCount() {
  const context = React.useContext(CountContext)
  if (!context) {
    throw new Error(`useCount must be used within a CountProvider`)
  }
  const [state, dispatch] = context

  const increment = () => dispatch({type: 'INCREMENT'})
  return {
    state,
    dispatch,
    increment,
  }
}

// export {CountProvider, useCount}

////////////////

// src/count/page.js

// import {CountProvider, useCount} from './count-context'

function Counter() {
  const {
    state: {count},
    increment,
  } = useCount()
  return <button onClick={increment}>{count}</button>
}

function CountDisplay() {
  const {
    state: {count},
  } = useCount()
  return <div>The current counter count is {count}</div>
}

function App() {
  return (
    <div>
      <CountProvider>
        <CountDisplay />
        <Counter />
      </CountProvider>
    </div>
  )
}

export default App
``` 
1. Not everything in your application needs to be in a single state object. Keep things logically separated (user settings does not necessarily have to be in the same context as notifications).
2. You will have multiple providers with this approach. Not all of your context needs to be globally accessible! **Keep state as close to where it's needed as possible.**

:memo: **참고 자료**
* [https://kentcdodds.com/blog/application-state-management-with-react](https://kentcdodds.com/blog/application-state-management-with-react)   
* [https://reactjs.org/docs/lifting-state-up.html](https://reactjs.org/docs/lifting-state-up.html)   
* [https://kentcdodds.com/blog/prop-drilling](https://kentcdodds.com/blog/prop-drilling)

