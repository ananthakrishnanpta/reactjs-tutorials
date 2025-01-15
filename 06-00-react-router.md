# **React Router v6 Tutorial**

---

## **Table of Contents**

1. [Introduction to Routing](#1-introduction-to-routing)  
2. [Setting Up React Router](#2-setting-up-react-router)  
3. [Configuring the Routes](#3-configuring-the-routes)  
4. [Adding Links for Routes](#4-adding-links-for-routes)  
5. [Handling Non-Existent Pages](#5-handling-non-existent-pages)  
6. [Reading URL Params](#6-reading-url-params)  
7. [Fetching Data](#7-fetching-data)  
8. [Navigate Programmatically](#8-navigate-programmatically)  
9. [Handling Query Params](#9-handling-query-params)  
10. [Nested Routes](#10-nested-routes)  
11. [Index Routes](#11-index-routes)  
12. [Assignment](#12-assignment)

---

## **1. Introduction to Routing**

Routing in web development is the process of determining which content to display based on the URL in the browser. In traditional multi-page websites, each page corresponds to a URL and is loaded separately. However, in modern single-page applications (SPAs) like those built with React, we need a way to **navigate** between different views without reloading the entire page.

**React Router** is a popular library that allows you to add routing to your React applications. It enables you to navigate between different components based on the URL, giving the user the experience of navigating a traditional multi-page website within the confines of a single page.

### **Key Concepts:**
- **Route**: A mapping between a URL and a component.
- **Navigation**: The act of moving between different views or components.
- **URL**: The unique address used to access a resource (view).

---

## **2. Setting Up React Router**

To use React Router in your project, you first need to install it.

### **Installation:**

1. Open your terminal and navigate to your project directory.
2. Run the following command to install React Router:

```bash
npm install react-router-dom@6
```

This will install the React Router v6 package, which contains everything you need to handle routing in your React application.

---

## **3. Configuring the Routes**

Once you’ve installed React Router, you need to set up the routes for your application. React Router provides several components to help you manage your routes.

### **Basic Route Setup:**

To configure routes, you use the following components:
- **BrowserRouter**: A wrapper component that keeps the UI in sync with the URL.
- **Routes**: A container for the `<Route>` components.
- **Route**: Defines a route that maps a URL path to a component.

#### **Example of Basic Route Setup:**

```jsx
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function Home() {
  return <h2>Home Page</h2>;
}

function About() {
  return <h2>About Page</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

In this example:
- **Home** and **About** are two different components that correspond to different paths (`/` and `/about` respectively).
- The `element` prop of the `Route` specifies the component to render when the route is matched.

---

## **4. Adding Links for Routes**

To navigate between different routes, you need to provide links. React Router provides the `Link` component to help you create links to different routes.

### **Adding Links Using the `Link` Component:**

```jsx
import { Link } from 'react-router-dom';

function Navigation() {
  return (
    <nav>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About</Link></li>
      </ul>
    </nav>
  );
}
```

Here, the `Link` component helps you create clickable links. The `to` prop defines the path to navigate to.

You can use these links inside any component to enable navigation between different routes in your application.

---

## **5. Handling Non-Existent Pages**

What happens if the user tries to visit a route that doesn’t exist in your application? React Router provides a mechanism to handle these "404" or non-existent routes.

### **Handling 404 Pages:**

You can define a **catch-all route** that matches any undefined URL path and displays a 404 error or a custom message.

```jsx
function NotFound() {
  return <h2>404 - Page Not Found</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="*" element={<NotFound />} /> {/* Catch-all route */}
      </Routes>
    </BrowserRouter>
  );
}
```

Here, the `path="*"` route will match any path that doesn’t match the previous routes and will display the `NotFound` component.

---

## **6. Reading URL Params**

URL parameters allow you to pass dynamic data in the URL. For example, if you have a list of products, each product might have a unique `id`. You can capture these dynamic parts of the URL and use them in your application.

### **Reading URL Params:**

```jsx
import { useParams } from 'react-router-dom';

function Product() {
  const { id } = useParams(); // Capture the product id from the URL

  return <h2>Product ID: {id}</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/product/:id" element={<Product />} />
      </Routes>
    </BrowserRouter>
  );
}
```

In this example, the `:id` part of the URL is a dynamic parameter. Using `useParams()`, you can extract the `id` from the URL.

---

## **7. Fetching Data**

In React, it is common to fetch data based on the URL parameters. For example, you might need to fetch product details based on the product ID from the URL.

### **Fetching Data Based on URL Params:**

```jsx
import { useParams } from 'react-router-dom';
import { useEffect, useState } from 'react';

function Product() {
  const { id } = useParams();
  const [product, setProduct] = useState(null);

  useEffect(() => {
    // Simulating a fetch request based on the product id
    fetch(`https://fakestoreapi.com/products/${id}`)
      .then(response => response.json())
      .then(data => setProduct(data));
  }, [id]); // Re-fetch data when id changes

  if (!product) return <p>Loading...</p>;

  return (
    <div>
      <h2>{product.title}</h2>
      <p>{product.description}</p>
    </div>
  );
}
```

Here, the product is fetched from an API based on the `id` parameter in the URL. The `useEffect` hook is used to trigger the fetch operation when the component is mounted or when the `id` changes.

---

## **8. Navigate Programmatically**

Sometimes you might need to navigate programmatically based on certain conditions. React Router provides the `useNavigate` hook for this purpose.

### **Using `useNavigate` to Navigate Programmatically:**

```jsx
import { useNavigate } from 'react-router-dom';

function RedirectButton() {
  const navigate = useNavigate();

  const handleClick = () => {
    navigate('/about'); // Navigate to the About page programmatically
  };

  return <button onClick={handleClick}>Go to About Page</button>;
}
```

When the button is clicked, `navigate('/about')` will programmatically redirect the user to the `/about` route.

---

## **9. Handling Query Params**

Query parameters are additional pieces of information appended to the URL after a question mark (`?`). React Router allows you to read and manipulate query params easily.

### **Reading Query Params:**

```jsx
import { useLocation } from 'react-router-dom';

function SearchResults() {
  const location = useLocation();
  const queryParams = new URLSearchParams(location.search);
  const searchTerm = queryParams.get('q');

  return <h2>Searching for: {searchTerm}</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/search" element={<SearchResults />} />
      </Routes>
    </BrowserRouter>
  );
}
```

In this example, `useLocation` gives access to the current URL, including the query string. The `URLSearchParams` API helps you extract the value of query parameters.

---

## **10. Nested Routes**

Nested routes allow you to render components inside other components. This is useful when you want to display child routes within a parent route.

### **Using Nested Routes:**

```jsx
function Dashboard() {
  return (
    <div>
      <h2>Dashboard</h2>
      <Outlet /> {/* Renders nested routes */}
    </div>
  );
}

function Profile() {
  return <h3>Profile Page</h3>;
}

function Settings() {
  return <h3>Settings Page</h3>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />}>
          <Route path="profile" element={<Profile />} />
          <Route path="settings" element={<Settings />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

Here, `Dashboard` is a parent component, and `Profile` and `Settings` are nested routes. The `Outlet` component renders the nested routes inside the `Dashboard` component.

---

## **11. Index Routes**

An **index route** is a default route for a parent route, rendered when the parent route matches but no specific child route is defined.

### **Using Index Routes:**

```jsx
function Dashboard() {
  return <h2>Welcome to the Dashboard</h2>;
}

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/dashboard" element={<Dashboard />}>
          <Route index element={<h3>Dashboard Home</h3>} /> {/* Default route for Dashboard */}
        </Route>
      </Routes>
    </BrowserRouter>
  );
}
```

In this example, the `index` route inside the `Dashboard` route will render when the user visits `/dashboard`.

---

## **12. Assignment**

1. **Create a Blog**: Create a simple blog where users can view posts and visit each post page using dynamic routes (e.g., `/post/:id`).

2. **Build a Product Catalog**: Create a product catalog with the ability to filter products using query parameters, e.g., `/products?q=shoes`.

3. **Implement Nested Routes**: Create a dashboard with nested routes for profile and settings. Use the `Outlet` component to render nested views.
