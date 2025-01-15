# **ReactJS Tutorial: Handling Side-Effects**

---

## **Table of Contents**

1. [What is a Side-Effect?](#1-what-is-a-side-effect)  
2. [Pure vs Impure Functions](#2-pure-vs-impure-functions)  
3. [Side-Effect in React](#3-side-effect-in-react)  
4. [Cleanup of a Side-Effect](#4-cleanup-of-a-side-effect)  
5. [How to Handle Side-Effect in Class Component](#5-how-to-handle-side-effect-in-class-component)  
6. [Add a Side-Effect in Our Github Cards Project](#6-add-a-side-effect-in-our-github-cards-project)  
7. [Project: Building E-Commerce Application](#7-project-building-e-commerce-application)  
8. [Assignment](#8-assignment)

---

## **1. What is a Side-Effect?**

A **side-effect** refers to any operation that affects the outside world, beyond just returning a value. In the context of React, side-effects are actions that can trigger changes outside the component’s rendering logic. These can include:

- **API requests** to fetch data from a server.
- **Updating the browser’s local storage**.
- **Interacting with timers**, such as setting intervals or timeouts.
- **Modifying the DOM** directly outside of React's render cycle.

Side-effects in React typically occur after the initial render or when state or props change, and they often require special handling to ensure the application remains efficient and functional.

---

## **2. Pure vs Impure Functions**

### **What are Pure Functions?**

A **pure function** is a function that always produces the same output for the same input and does not cause any side-effects. It does not rely on or modify any external state or variables.

**Characteristics of Pure Functions:**
- The output is determined only by the input.
- They do not alter any external state or variables.
- They do not perform any side-effects like making HTTP requests or modifying the DOM.

**Example of a Pure Function:**
```js
function add(a, b) {
  return a + b;
}
```

Here, the function `add()` always returns the same result for the same inputs, with no side-effects.

### **What are Impure Functions?**

An **impure function**, on the other hand, interacts with external systems, like making HTTP requests, or it modifies variables outside its scope, leading to side-effects.

**Example of an Impure Function:**
```js
function logToConsole(message) {
  console.log(message);  // Side-effect
  return message;
}
```

Here, `logToConsole()` is impure because it interacts with the outside world (console logging) and doesn't just return a value.

---

## **3. Side-Effect in React**

In React, components typically render UI based on state and props. However, sometimes you need to perform side-effects during the lifecycle of a component. For example, you might need to fetch data when a component mounts or handle an event after user input.

### **Why is Side-Effect Handling Important in React?**

Without proper handling, side-effects in React can lead to:

- **Performance issues**: Unnecessary network requests, unnecessary DOM manipulation.
- **Memory leaks**: Not cleaning up after side-effects, leading to unneeded resource consumption.
- **UI inconsistencies**: Components might render incorrectly if side-effects are handled improperly.

React provides two ways to handle side-effects:
1. **In functional components**, using the `useEffect` hook.
2. **In class components**, using lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.

---

## **4. Cleanup of a Side-Effect**

One of the most important aspects of managing side-effects in React is **cleanup**. A cleanup function allows you to perform necessary actions before the component is removed from the DOM or before the effect is re-run.

### **Why Cleanup Is Important?**
For example, if you're setting up an interval in a side-effect, it’s important to clear the interval when the component is unmounted or the effect dependencies change. Failing to clean up side-effects can lead to memory leaks, unnecessary operations, and application slowdowns.

### **How to Clean Up Side-Effects?**

In **functional components**, cleanup is done inside the `useEffect` hook by returning a function.

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    console.log('Timer is running');
  }, 1000);

  // Cleanup the timer when the component is unmounted
  return () => clearInterval(timer);
}, []); // Empty dependency array means the effect runs once (componentDidMount equivalent)
```

In **class components**, you use the `componentWillUnmount` lifecycle method to perform the cleanup.

---

## **5. How to Handle Side-Effect in Class Component**

### **Handling Side-Effect in Class Components:**

In class components, side-effects are handled using lifecycle methods, particularly:
- `componentDidMount`: Called after the component is rendered, useful for side-effects like fetching data.
- `componentDidUpdate`: Called after the component updates, useful for side-effects that depend on state or prop changes.
- `componentWillUnmount`: Called before the component is removed from the DOM, useful for cleanup tasks.

### **Example of Side-Effect in a Class Component:**

```jsx
class TimerComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { time: 0 };
  }

  componentDidMount() {
    this.timer = setInterval(() => {
      this.setState((prevState) => ({ time: prevState.time + 1 }));
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timer); // Cleanup the interval when the component is unmounted
  }

  render() {
    return <h1>Time: {this.state.time}</h1>;
  }
}

ReactDOM.render(<TimerComponent />, document.getElementById('root'));
```

Here:
- In `componentDidMount()`, we start a timer that updates the state every second.
- In `componentWillUnmount()`, we clean up by clearing the interval to avoid memory leaks.

---

## **6. Add a Side-Effect in Our Github Cards Project**

In this section, we will add a side-effect to our Github Cards project to fetch user data from GitHub’s API.

### **Steps:**

1. We’ll use the `useEffect` hook to make the API request when the component mounts.
2. We’ll handle the cleanup of the API request to prevent memory leaks.
3. Display the user information once it's fetched.

```jsx
import React, { useState, useEffect } from 'react';

function GithubCards() {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch('https://api.github.com/users/octocat')
      .then((response) => response.json())
      .then((data) => {
        setUser(data);
        setLoading(false);
      })
      .catch((err) => {
        setError(err);
        setLoading(false);
      });

    // Cleanup function
    return () => {
      setUser(null); // Clear user data when component unmounts
    };
  }, []); // Empty dependency array means the effect runs once when component mounts

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error loading data: {error.message}</p>;

  return (
    <div>
      <h1>{user.name}</h1>
      <p>{user.bio}</p>
    </div>
  );
}

ReactDOM.render(<GithubCards />, document.getElementById('root'));
```

In this code:
- `useEffect` fetches data from GitHub's API once the component mounts.
- If there’s an error, it is captured and displayed.
- The cleanup function clears the user data when the component unmounts.

---

## **7. Project: Building E-Commerce Application**

### **Handling Side-Effects in an E-Commerce Application:**

In an **E-commerce** application, you might need to fetch data such as product lists or handle actions like adding/removing items from a cart. These actions are side-effects that should be managed properly.

### **Steps to Handle Side-Effects in the E-Commerce App:**

1. **Fetching Products**:
   Use `useEffect` to fetch product data from an API.
   
2. **Updating the Cart**:
   When an item is added or removed from the cart, the application should update the state.

3. **Handling Cleanup**:
   If you have actions like setting timeouts or intervals (e.g., session timers), ensure they are cleared when the component unmounts.

### **Example:**

```jsx
function Products() {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch('https://api.example.com/products')
      .then((response) => response.json())
      .then((data) => {
        setProducts(data);
        setLoading(false);
      })
      .catch((err) => {
        console.error('Error fetching products', err);
        setLoading(false);
      });

    return () => {
      setProducts([]); // Cleanup product data
    };
  }, []);

  if (loading) return <p>Loading products...</p>;

  return (
    <div>
      {products.map((product) => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>{product.description}</p>
        </div>
      ))}
    </div>
  );
}
```

Here:
- We fetch the products when the component mounts.
- We handle any errors and perform cleanup by clearing the products when the component unmounts.

---

## **8. Assignment**

1. **Handling Fetch Side-Effect**:
   - Create a component that fetches data from a public API (e.g., weather data) when the component mounts and displays it. 
   - Ensure to handle errors and loading states properly.

2. **Timer Cleanup**:
   - Create a countdown timer that starts when the component mounts and cleans up the timer when the component unmounts.
   - Use `componentWillUnmount` for class components or `useEffect` cleanup for functional components.

3. **E-Commerce Cart**:
   - Create a simple shopping cart component that adds and removes items. Make sure to clean up the cart data when the user navigates away or closes the browser.

---
