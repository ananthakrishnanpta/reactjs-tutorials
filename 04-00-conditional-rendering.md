# **ReactJS Tutorial: Displaying Components and Conditional Rendering (Elaborate Version)**

---

## **Table of Contents**

1. [Displaying Components](#1-displaying-components)  
2. [Conditional Rendering](#2-conditional-rendering)  
3. [Short Circuiting](#3-short-circuiting)  
4. [Logical Operators](#4-logical-operators)  
5. [Ternary Operators](#5-ternary-operators)  
6. [Assignment](#6-assignment)

---

## **1. Displaying Components**

### **What Are Components in React?**
In React, **components** are the fundamental building blocks of a React application. Each component is a JavaScript function or class that returns a part of the UI. Components are used to break the UI into smaller, reusable pieces, making the code more modular and maintainable.

#### **Rendering a Component:**

To render a React component, we use the `ReactDOM.render()` function. This is where we define the root of our application and specify the component we want to render.

**Example:**
```jsx
function Welcome() {
  return <h1>Welcome to React!</h1>;
}

ReactDOM.render(<Welcome />, document.getElementById('root'));
```

Here:
- The `Welcome` function component is defined.
- Inside the component, we return an `<h1>` element with the text "Welcome to React!".
- `ReactDOM.render(<Welcome />, document.getElementById('root'))` is used to render this component into the DOM.

### **Displaying Dynamic Content in Components:**
React allows you to display dynamic content based on the component's **state** or **props**. These values can change over time, and React will automatically update the rendered content when the state or props change.

---

## **2. Conditional Rendering**

### **What is Conditional Rendering?**
**Conditional rendering** refers to the ability to display different UI elements or components based on certain conditions. React provides several ways to perform conditional rendering within a component, such as using **if-else** statements, **ternary operators**, and **logical operators**.

### **Using `if-else` Statements:**
Although you cannot use traditional `if-else` statements directly inside JSX, you can use them outside of the JSX expression and return different components accordingly.

#### **Example Using `if-else`:**

```jsx
function Greeting({ isLoggedIn }) {
  if (isLoggedIn) {
    return <h1>Welcome back!</h1>;
  } else {
    return <h1>Please log in.</h1>;
  }
}

ReactDOM.render(<Greeting isLoggedIn={true} />, document.getElementById('root'));
```

Here:
- The `Greeting` component accepts a prop called `isLoggedIn`.
- If `isLoggedIn` is `true`, it renders a welcoming message.
- Otherwise, it renders a message prompting the user to log in.

### **Using Ternary Operator for Conditional Rendering:**
The **ternary operator** provides a shorthand for `if-else` conditions and is very useful in JSX.

#### **Example Using Ternary Operator:**

```jsx
function Greeting({ isLoggedIn }) {
  return (
    <h1>{isLoggedIn ? 'Welcome back!' : 'Please log in.'}</h1>
  );
}

ReactDOM.render(<Greeting isLoggedIn={false} />, document.getElementById('root'));
```

Here:
- The ternary operator checks whether `isLoggedIn` is `true`. If it is, it renders "Welcome back!"; if not, it renders "Please log in."
- Ternary operators are a concise way to handle conditional rendering within JSX.

---

## **3. Short Circuiting**

### **What is Short Circuiting?**
**Short circuiting** is a technique where logical operators, such as **AND (`&&`)** and **OR (`||`)**, are used to evaluate conditions. These operators can be used for **conditional rendering** in React.

#### **Using Logical AND (`&&`) for Short Circuiting:**
The logical AND (`&&`) operator allows you to render an element only when a condition is `true`. If the condition is `false`, nothing will be rendered.

#### **Example Using AND (`&&`):**
```jsx
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {isLoggedIn && <h1>Welcome back!</h1>}
    </div>
  );
}

ReactDOM.render(<Greeting isLoggedIn={true} />, document.getElementById('root'));
```

Here:
- If `isLoggedIn` is `true`, it renders `<h1>Welcome back!</h1>`.
- If `isLoggedIn` is `false`, nothing is rendered, making the use of `&&` a powerful short-circuiting technique for conditional rendering.

### **Using Logical OR (`||`) for Default Values:**
The `||` operator can be used for **default value rendering**. If the first condition is **falsy** (e.g., `null`, `undefined`, `false`, `0`, or an empty string), the second value is rendered as a fallback.

#### **Example Using OR (`||`):**

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name || 'Guest'}!</h1>;
}

ReactDOM.render(<Greeting />, document.getElementById('root'));
```

Here:
- If the `name` prop is falsy (e.g., undefined or null), it will default to `'Guest'`.
- The `||` operator allows you to define fallback behavior for when the primary value is not provided.

---

## **4. Logical Operators**

### **Using Logical NOT (`!`) Operator:**
The **logical NOT** (`!`) operator is useful for checking if a value is falsy. When applied to a condition, it inverts the boolean value, which can be used to conditionally render elements.

#### **Example Using Logical NOT (`!`):**
```jsx
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {!isLoggedIn && <h1>Please log in.</h1>}
    </div>
  );
}

ReactDOM.render(<Greeting isLoggedIn={false} />, document.getElementById('root'));
```

Here:
- If `isLoggedIn` is `false`, it renders the message "Please log in."
- The logical NOT operator is used to invert the `isLoggedIn` condition.

### **Combining Logical Operators:**
You can combine multiple logical operators to create more complex conditions.

#### **Example:**
```jsx
function Greeting({ age, isLoggedIn }) {
  return (
    <div>
      {age >= 18 && isLoggedIn && <h1>Welcome, Adult!</h1>}
      {age < 18 && <h1>You are underage</h1>}
    </div>
  );
}

ReactDOM.render(<Greeting age={20} isLoggedIn={true} />, document.getElementById('root'));
```

Here:
- If the user is `18 or older` and `isLoggedIn` is `true`, it displays "Welcome, Adult!".
- If the user is underage (`age < 18`), it displays "You are underage".
- This shows how logical operators can be combined to form more complex conditions.

---

## **5. Ternary Operators**

### **What is a Ternary Operator?**
The **ternary operator** is a shorthand version of an `if-else` statement. It allows you to return one of two expressions based on a condition.

### **Syntax:**
```jsx
condition ? expressionIfTrue : expressionIfFalse
```

### **Using Ternary Operator in JSX:**

#### **Example:**
```jsx
function Greeting({ isLoggedIn }) {
  return (
    <h1>{isLoggedIn ? 'Welcome back!' : 'Please log in.'}</h1>
  );
}

ReactDOM.render(<Greeting isLoggedIn={true} />, document.getElementById('root'));
```

Here:
- The ternary operator checks if `isLoggedIn` is `true`. If it is, it renders "Welcome back!". If it is `false`, it renders "Please log in."

### **Rendering Different Components Based on Conditions:**
You can use ternary operators to render entire components conditionally.

#### **Example:**
```jsx
function App() {
  const isLoggedIn = true;

  return (
    <div>
      {isLoggedIn ? <Dashboard /> : <Login />}
    </div>
  );
}

function Dashboard() {
  return <h2>Welcome to your Dashboard!</h2>;
}

function Login() {
  return <h2>Please log in to continue.</h2>;
}

ReactDOM.render(<App />, document.getElementById('root'));
```

Here:
- If `isLoggedIn` is `true`, it renders the `Dashboard` component.
- If `isLoggedIn` is `false`, it renders the `Login` component.

---

## **6. Assignment**

1. **Display a Message Based on Age:**
   - Create a component called `AgeCheck` that accepts an `age` prop.
   - If the `age` is `18` or older, render the message "You are an adult".
   - If the `age` is less than `18`, render "You are a minor".

2. **Conditional Display of VIP Status:**
   - Create a component that accepts a `isVIP` prop.
   - If `isVIP` is `true`, render the message "Welcome, VIP!".
   - If `isVIP` is `false`, render "You are a regular user.".

3. **Rendering Different Components with Ternary Operator:**
   - Create a component `UserStatus` that conditionally renders the `LoggedIn` or `Guest` components using a ternary operator based on the `isLoggedIn` state.

---
