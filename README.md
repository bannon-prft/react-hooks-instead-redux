# Replacing Redux with React Hooks

This project is a practice project from [React - The Complete Guide (incl Hooks, React Router, Redux)](https://www.udemy.com/course/react-the-complete-guide-incl-redux/)

## Context API

Context API is sometimes the right approach but not always
- Performance not optimized for state that changes frequently
- Would be good for something like authentication
- Has no clever way of figuring out which components actually use the context so when the context is updated, every component tht uses the context is rebuilt, whether it is directly affected or not

## Custom Hooks

Can manage a global state in a separate file from the components and create the state only once and re-use it within all of the components

State is shared within the entire application

Every component that uses the custom hook uses the same values for `gloablState`, `listeners`, `actions`

Register one listener per component

Can initialize store multiple times by using the current globalState and current actions map to merge the new data



```js
import { useEffect, useState } from 'react'

let globalState = {}
let listeners = []
let actions = {}

export const useStore = () => {
  const setState = useState(globalState)[1]

  const dispatch = (actionIdentifier, payload) => {
    const newState = actions[actionIdentifier](globalState, payload)
    globalState = { ...globalState, ...newState }

    for (const listener of listeners) {
      listener(globalState)
    }
  }

  useEffect(() => {
    listeners.push(setState)

    // cleanup
    return () => {
      listeners = listeners.filter((li) => li !== setState)
    }
  }, [])

  return [globalState, dispatch]
}

export const initStore = (userActions, initialState) => {
  if (initialState) {
    globalState = { ...globalState, initialState }
  }
  actions = { ...action, ...userActions }
}
```
Because `useStore` is a closure, the value of `setState` is captured for the component that's using the custom hook and will be the same when the component unmounts as when it mounts


