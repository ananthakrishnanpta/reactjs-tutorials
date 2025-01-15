# **ReactJS Tutorial: Components, Props, and State**

---

## **Table of Contents**

1. [Component](#1-component)  
2. [Function Component & Props](#2-function-component--props)  
3. [Class Component & Props](#3-class-component--props)  
4. [Counter App](#4-counter-app)  
5. [Summary & Challenges](#5-summary--challenges)  
6. [Assignment](#6-assignment)  

---

## **1. Component**

### **What is a Component?**
In React, a **Component** is a self-contained, reusable piece of code that defines part of the UI. Each component has its own **state** and **props**, and it can be nested inside other components to build a complete UI. Components are the building blocks of React applications.

### **Types of Components:**
- **Function Components**: These are simple JavaScript functions that accept props as arguments and return JSX.
- **Class Components**: These are ES6 classes that extend `React.Component` and must have a `render()` method.

React encourages using function components, especially with the introduction of **Hooks** (which we'll cover later). However, class components are still widely used in older codebases.

### **Creating a Simple Component:**
A component can be created as a function or class, depending on the requirements. Here's how both types look:

#### **Function Component Example**:
```jsx
function Welcome() {
  return <h1>Hello, World!</h1>;
}

ReactDOM.render(<Welcome />, document.getElementById('root'));
```

#### **Class Component Example**:
```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, World!</h1>;
  }
}

ReactDOM.render(<Welcome />, document.getElementById('root'));
```

Both examples achieve the same result, but function components are generally more concise and recommended for new code.

---

## **2. Function Component & Props**

### **What Are Props?**
Props (short for **properties**) are the mechanism by which data is passed from a **parent component** to a **child component**. Props allow you to customize the behavior or appearance of a child component.

### **Using Props in Function Components:**
A function component accepts **props** as an argument and can access the values passed to it.

#### **Example:**
```jsx
function Greeting(props) {
  return <h1>Hello, {props.name}!</h1>;
}

function App() {
  return <Greeting name="React Learner" />;
}

ReactDOM.render(<App />, document.getElementById('root'));
```

Here:
- `Greeting` is a **function component**.
- `name` is a **prop** passed to the `Greeting` component by its parent, `App`.
- `{props.name}` is used to render the value of the `name` prop.

### **Default Props**:
You can also set default values for props in case they are not passed from the parent:
```jsx
function Greeting(props) {
  return <h1>Hello, {props.name}</h1>;
}

Greeting.defaultProps = {
  name: "Guest"
};

ReactDOM.render(<Greeting />, document.getElementById('root'));
```

Here, if no `name` prop is passed, the default value "Guest" will be used.

---

## **3. Class Component & Props**

### **Class Components and Props**:
In class components, props are accessed through `this.props`. Class components also have lifecycle methods, which provide more control over the component's behavior, especially for more complex applications.

#### **Example:**
```jsx
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}

ReactDOM.render(<Greeting name="React Learner" />, document.getElementById('root'));
```

Here:
- `this.props.name` is used to access the `name` prop in the class component.
- The `Greeting` class component is rendered by passing the `name` prop from the parent.

### **Why Use Class Components?**
Class components provide features like lifecycle methods (`componentDidMount`, `componentWillUnmount`) and **state management** (which we will cover next). However, with the introduction of **Hooks**, function components can now also manage state and lifecycle methods, making them the preferred option for new projects.

---

## **4. Counter App**

Let’s create a **Counter App** to understand how props and state work together in React.

### **What is State?**
State represents the data that can change over time in a component. When the state changes, the component re-renders to reflect the new state. State is **local** to the component and can be passed down to child components via props.

### **Creating a Counter App**:

#### **Step 1: Create the Counter Component**

In the following example, we'll create a **function component** with `useState` (a React hook) to manage the count state:

```jsx
function Counter() {
  const [count, setCount] = React.useState(0);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);

  return (
    <div>
      <h1>Counter: {count}</h1>
      <button onClick={increment}>Increment</button>
      <button onClick={decrement}>Decrement</button>
    </div>
  );
}

ReactDOM.render(<Counter />, document.getElementById('root'));
```

Here:
- We use `useState(0)` to declare the state variable `count` and initialize it to 0.
- `setCount` is used to update the `count` state.
- The `increment` and `decrement` functions modify the state when the respective buttons are clicked.

#### **Step 2: Running the App**

- The component renders the `count` value inside an `<h1>` tag.
- Clicking the **Increment** button increases the count, and clicking the **Decrement** button decreases it.
- Every time the state changes, React re-renders the component to reflect the new count.

---

## **5. Summary & Challenges**

### **Summary**
- **Components** are the building blocks of React applications.
  - Function components and class components are two types of components.
  - Function components are easier to write and are the preferred choice with the use of hooks.
- **Props** are used to pass data from a parent component to a child component.
  - Props are immutable and used for passing down static data to child components.
  - Default props can be set to provide fallback values.
- **State** represents mutable data within a component.
  - State can be modified by user interactions, and when state changes, the component re-renders.

### **Challenges**
1. Create a function component that accepts `name`, `age`, and `location` as props and displays them inside a `<p>` tag.
2. Modify the Counter app to include a **Reset** button that sets the count back to 0.
3. Create a component that takes a **message** prop and displays it. If no `message` is provided, it should display a default message: "No message available."

---

## **6. Assignment**

1. **Create a Greeting Component**:
   - Create a function component called `Greeting`.
   - Pass a prop `name` to it and display a personalized greeting (e.g., "Hello, John!").
   - Use default props to display "Hello, Guest!" if no name is passed.

2. **Class Component with Props**:
   - Create a class component called `UserProfile`.
   - Pass `name` and `age` as props and display them in an `<h2>` tag and `<p>` tag respectively.

3. **Counter App with Reset Button**:
   - Enhance the `Counter` app by adding a **Reset** button that resets the counter to zero when clicked.

---
