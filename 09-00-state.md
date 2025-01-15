# **React Context & Reducers Tutorial**

---

## **1. Introduction to Context & Reducers**

React provides several ways to manage state. The simplest way is using `useState` for local component state. However, when managing state across many components, things can get complicated. This is where **Context API** and **Reducers** come in. 

- **Context** allows you to pass data through the component tree without manually passing props down at every level.
- **Reducers** are functions that manage state based on specific actions, like how they work in Redux. They allow more structured and predictable state management.

---

## **2. What is Context API?**

The **Context API** is a React feature that enables you to share state between components without using `props`. It allows you to create a global state, which can be accessed from any component in the tree without the need to pass it down manually. 

It consists of three main parts:
- **React.createContext()**: This creates a context object.
- **Provider**: The `Provider` component is used to provide the context value to its children.
- **Consumer**: The `Consumer` component (or `useContext` hook) is used to access the context value.

---

## **3. Creating Context and Provider**

Let’s first set up the Context API. We will create a context and a provider to hold and provide the state to child components.

### **Example: Creating Context & Provider**

Let’s imagine we want to create a theme context where we store a theme setting (light or dark) globally.

### **ThemeContext.js (Creating Context & Provider)**

```jsx
import React, { createContext, useState } from 'react';

// Create a context for theme
const ThemeContext = createContext();

// ThemeProvider component will provide the context value
export const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState('light'); // default theme is 'light'

  const toggleTheme = () => {
    setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light')); // toggling between 'light' and 'dark'
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export default ThemeContext;
```

### **Explanation:**
- **`createContext`**: Creates a context object that will be used to share the state.
- **`useState`**: Manages the theme state, starting with the value `'light'`.
- **`ThemeContext.Provider`**: This component wraps our app (or any other component) and provides the current `theme` and the `toggleTheme` function to the components inside it.

The `ThemeProvider` is responsible for providing the context value (`theme` and `toggleTheme`) to the component tree.

---

## **4. Accessing Context Values**

Now that we have set up the `ThemeProvider` and context, we can access the context values in any child component using the `useContext` hook.

### **App.js (Accessing Context Values)**

```jsx
import React, { useContext } from 'react';
import { ThemeProvider } from './ThemeContext';
import ThemeToggler from './ThemeToggler';

function App() {
  return (
    <ThemeProvider>
      <div className="App">
        <h1>React Context API Example</h1>
        <ThemeToggler />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

### **ThemeToggler.js (Using `useContext` to Access the Context)**

```jsx
import React, { useContext } from 'react';
import ThemeContext from './ThemeContext';

function ThemeToggler() {
  const { theme, toggleTheme } = useContext(ThemeContext); // Using useContext to access context values

  return (
    <div>
      <h2>Current Theme: {theme}</h2>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

export default ThemeToggler;
```

### **Explanation:**
- **`useContext`**: This hook accesses the value from the `ThemeContext`. It extracts both `theme` and `toggleTheme`.
- **`toggleTheme`**: When the button is clicked, the theme is toggled between `'light'` and `'dark'`.

---

## **5. Custom Context Hook**

While `useContext` is great for accessing context values, sometimes you may want to simplify the context usage further. A custom context hook can make it easier to use context across your app.

### **Custom Hook for Theme Context**

```jsx
import { useContext } from 'react';
import ThemeContext from './ThemeContext';

export const useTheme = () => {
  return useContext(ThemeContext);
};
```

### **App.js (Using Custom Hook)**

```jsx
import React from 'react';
import { ThemeProvider } from './ThemeContext';
import ThemeToggler from './ThemeToggler';
import { useTheme } from './useTheme';

function App() {
  const { theme } = useTheme(); // Using the custom hook

  return (
    <ThemeProvider>
      <div className={`App ${theme}`}>
        <h1>React Context API with Custom Hook</h1>
        <ThemeToggler />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

### **Explanation:**
- The custom hook `useTheme()` abstracts away the need to call `useContext` directly in each component, which can be helpful if the context is used in many places.

---

## **6. Understanding Reducer**

Reducers are functions that handle state changes in a predictable way. They are typically used with the `useReducer` hook. Reducers work by receiving the current state and an action, and they return the new state.

In React, a **reducer** is especially useful when dealing with complex state logic, like toggling multiple states or handling a series of related state updates.

### **Example: Reducer Function**

A reducer function typically has two arguments:
1. **`state`**: The current state.
2. **`action`**: An object describing what happened.

```jsx
const themeReducer = (state, action) => {
  switch (action.type) {
    case 'TOGGLE_THEME':
      return { theme: state.theme === 'light' ? 'dark' : 'light' };
    default:
      return state;
  }
};
```

### **Explanation:**
- The reducer receives the `state` and `action`. If the `action.type` is `'TOGGLE_THEME'`, it toggles the theme between `'light'` and `'dark'`.

---

## **7. Summary**

- **Context API** is useful for sharing global state across components without needing to pass props down through multiple levels.
- **useContext** is the hook used to consume the context in a component.
- **Reducers** help you handle state changes in a more predictable and structured way, especially in complex scenarios.
- By combining **React Context** and **Reducers**, we can manage complex state across large applications without prop drilling or excessive use of `useState`.

---

## **8. Assignment**

1. **Add Multiple States**: Extend the theme example to manage multiple global states (e.g., user authentication state, language preference, etc.).
2. **Reducer with Multiple Actions**: Modify the `themeReducer` to handle multiple actions such as `SET_THEME`, `RESET_THEME`, etc.
3. **Global State with Multiple Providers**: Create multiple contexts (e.g., for theme, language, and authentication), and combine them in the App.
4. **Styling**: Use the state to change styles globally across components, such as switching themes and applying conditional styling.

---
