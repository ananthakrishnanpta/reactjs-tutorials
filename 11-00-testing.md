# **Testing in React**

---

## **1. Overview of Testing Libraries: Jest, React Testing Library**

### **What is Jest?**
[Jest](https://jestjs.io/) is a JavaScript testing framework commonly used with React. It is designed to test JavaScript code, including React components, in a simple and effective way.

Jest provides:
- **Test runners**: Automatically run your tests.
- **Assertion library**: To make sure that the behavior of your code is as expected (e.g., `expect()` function).
- **Mocking capabilities**: Mocking functions and modules to test components in isolation.
- **Snapshot testing**: To ensure UI consistency.

### **What is React Testing Library?**
[React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) is a set of utilities to help test React components in a way that focuses on user interaction rather than implementation details. It encourages testing from the user’s perspective.

Unlike other testing libraries, React Testing Library is designed to test how the component behaves in a real environment rather than testing internal implementation.

### **Why use these libraries together?**
- **Jest**: Provides the framework and tools to run the tests, handle mocking, and do assertions.
- **React Testing Library**: Focuses on testing React components by interacting with them as users would.

---

## **2. Writing Unit Tests for React Components**

Unit tests verify individual components in isolation, ensuring that each component behaves correctly given specific inputs and outputs.

### **Setting Up Jest and React Testing Library**

To start testing React components, you need to set up Jest and React Testing Library in your project.

1. **Install Jest and React Testing Library:**
   If you're using Create React App, Jest and React Testing Library are already included. If not, you can install them manually:
   
   ```bash
   npm install --save-dev jest @testing-library/react @testing-library/jest-dom
   ```

2. **Setup Jest Configuration:**
   Jest can be configured in the `package.json` file or via a `jest.config.js` file. Here's the basic setup:
   
   ```json
   {
     "scripts": {
       "test": "jest"
     }
   }
   ```

### **Unit Test Example:**

Let’s say you have a simple component that takes a `name` prop and displays it.

```jsx
// Greeting.js
import React from 'react';

function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

export default Greeting;
```

Now let’s write a unit test to verify if this component renders the name prop correctly.

```jsx
// Greeting.test.js
import { render, screen } from '@testing-library/react';
import Greeting from './Greeting';

test('renders the correct greeting message', () => {
  render(<Greeting name="John" />);
  
  // Find the h1 element and check its text content
  const greetingElement = screen.getByText(/hello, john/i);
  expect(greetingElement).toBeInTheDocument();  // Assert that the element is in the DOM
});
```

### **Explanation:**
- **`render()`**: This renders the component into a virtual DOM for testing.
- **`screen.getByText()`**: This is a query to find an element by its text content.
- **`expect()`**: This is used to make assertions, i.e., verify if the component's behavior is correct.
- **`toBeInTheDocument()`**: This assertion checks if the element is present in the rendered output.

---

## **3. Snapshot Testing and Mocking Dependencies**

### **Snapshot Testing**
Snapshot testing allows you to capture the rendered output of a component at a given point in time and store it in a snapshot file. This file can then be compared with future renders to detect changes in the UI.

#### **Example of Snapshot Testing:**

```jsx
// Greeting.test.js
import { render } from '@testing-library/react';
import Greeting from './Greeting';
import { create } from 'react-test-renderer';

test('matches the snapshot', () => {
  const component = create(<Greeting name="John" />);
  
  // Create a snapshot of the rendered component
  let tree = component.toJSON();
  expect(tree).toMatchSnapshot();  // Compare with the stored snapshot
});
```

### **Explanation:**
- **`create()`**: This is from `react-test-renderer` and helps create a snapshot of the component.
- **`toMatchSnapshot()`**: This assertion compares the component’s rendered output with the stored snapshot.

When you run the test for the first time, Jest creates a snapshot file. If the UI of the component changes later, Jest will warn you if the rendered output does not match the snapshot.

### **Mocking Dependencies**
You can mock external dependencies (such as API calls or libraries) to isolate the component and test it without external dependencies.

#### **Example of Mocking a Module:**

Let’s say the `Greeting` component fetches the user's name from an API:

```jsx
// Greeting.js
import React, { useState, useEffect } from 'react';

function Greeting() {
  const [name, setName] = useState('');

  useEffect(() => {
    // Simulating an API call
    fetch('/api/user')
      .then((response) => response.json())
      .then((data) => setName(data.name));
  }, []);

  return <h1>Hello, {name}!</h1>;
}

export default Greeting;
```

We can mock the `fetch` call in the test like so:

```jsx
// Greeting.test.js
import { render, screen } from '@testing-library/react';
import Greeting from './Greeting';

// Mock the fetch function
global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve({ name: 'John' }),
  })
);

test('fetches and displays user name', async () => {
  render(<Greeting />);
  
  // Wait for the asynchronous update to be rendered
  const greetingElement = await screen.findByText(/hello, john/i);
  expect(greetingElement).toBeInTheDocument();
});
```

### **Explanation:**
- **`jest.fn()`**: This is used to mock the `fetch` function.
- **`Promise.resolve()`**: This simulates the resolved response from the API.
- **`findByText()`**: This is an async query that waits for the element to appear in the DOM.

---

## **4. Integration Testing with React Testing Library**

Integration tests focus on testing how various components interact with each other. React Testing Library is great for integration tests because it allows you to simulate user interactions and verify the app’s behavior.

#### **Example of an Integration Test:**

Let’s assume we have a `UserProfile` component that fetches data from an API and renders a `Greeting` component.

```jsx
// UserProfile.js
import React, { useState, useEffect } from 'react';
import Greeting from './Greeting';

function UserProfile() {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch('/api/user')
      .then((response) => response.json())
      .then((data) => setUser(data));
  }, []);

  if (!user) return <div>Loading...</div>;

  return <Greeting name={user.name} />;
}

export default UserProfile;
```

Now, let’s write an integration test for the `UserProfile` component, which relies on the `Greeting` component.

```jsx
// UserProfile.test.js
import { render, screen, waitFor } from '@testing-library/react';
import UserProfile from './UserProfile';

// Mock the fetch call to return a user object
global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve({ name: 'John' }),
  })
);

test('fetches and displays the user profile', async () => {
  render(<UserProfile />);

  // Wait for the user data to load and render the Greeting component
  await waitFor(() => screen.getByText(/hello, john/i));

  expect(screen.getByText(/hello, john/i)).toBeInTheDocument();
});
```

### **Explanation:**
- **`waitFor()`**: This ensures the test waits for the asynchronous operation (fetching user data) to complete.
- **`screen.getByText()`**: Once the data is fetched, this query ensures that the UI displays the correct content.

---

## **5. Assignment**

### **Assignment 1: Write Unit Tests**
- Write unit tests for the following components:
  - A `Counter` component that increments a number when a button is clicked.
  - A `UserList` component that renders a list of users.

### **Assignment 2: Implement Snapshot Testing**
- Write snapshot tests for a `ProfileCard` component that renders a user’s profile details (name, picture, etc.).

### **Assignment 3: Mocking API Calls**
- Write tests for a component that fetches user data from an API. Use Jest to mock the API response and test the component’s behavior.

---
