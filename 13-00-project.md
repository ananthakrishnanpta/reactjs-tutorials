# **React Shopping Site Tutorial: From Setup to Completion and Deployment**

### **Table of Contents:**
1. [Setting Up the Development Environment](#1-setting-up-the-development-environment)
2. [Creating a New React Project](#2-creating-a-new-react-project)
3. [Designing the Product Listings Page](#3-designing-the-product-listings-page)
4. [Implementing the Shopping Cart](#4-implementing-the-shopping-cart)
5. [Managing State with React Context](#5-managing-state-with-react-context)
6. [Adding a Checkout Page](#6-adding-a-checkout-page)
7. [Styling the Shopping Site](#7-styling-the-shopping-site)
8. [Deploying the App on Netlify](#8-deploying-the-app-on-netlify)
9. [Conclusion and Assignment](#9-conclusion-and-assignment)

---

## **1. Setting Up the Development Environment**

Before we start building our shopping site, we need to make sure that **Node.js** and **npm** (Node Package Manager) are installed on your system. These tools will help us set up the project and install necessary dependencies.

### **Step 1: Install Node.js & npm**
1. Go to the [Node.js Official Website](https://nodejs.org/en/download/) and download the latest LTS version.
2. After installing, open a terminal/command prompt and verify the installation:
   ```bash
   node -v
   npm -v
   ```
   You should see version numbers for both `Node.js` and `npm`.

---

## **2. Creating a New React Project**

We will use **`create-react-app`** to set up our React project. This tool sets up the React environment for you, so you don't have to worry about configurations.

### **Step 2: Create the Project**
1. In your terminal, run the following command to create a new React project:
   ```bash
   npx create-react-app shopping-site
   ```

2. Navigate to the newly created folder:
   ```bash
   cd shopping-site
   ```

3. Start the development server:
   ```bash
   npm start
   ```

   This will launch your app in `http://localhost:3000` in your browser.

---

## **3. Designing the Product Listings Page**

Now, let's add some products to our app. We will create a simple product listing page that displays products with their images, names, and prices.

### **Step 3: Create a Product Card Component**
1. Inside the `src` folder, create a new file `ProductCard.js`:

```jsx
import React from 'react';

function ProductCard({ product, onAddToCart }) {
  return (
    <div className="product-card">
      <img src={product.imageUrl} alt={product.name} />
      <h3>{product.name}</h3>
      <p>{product.price}</p>
      <button onClick={() => onAddToCart(product)}>Add to Cart</button>
    </div>
  );
}

export default ProductCard;
```

- **Explanation:**
  - The `ProductCard` component accepts a `product` prop and an `onAddToCart` callback.
  - It displays the product's image, name, price, and an "Add to Cart" button.

### **Step 4: Display Products in App.js**
1. Modify `App.js` to include a list of products and render `ProductCard` for each one:

```jsx
import React, { useState } from 'react';
import ProductCard from './ProductCard';

const products = [
  { id: 1, name: 'Product 1', price: '$10', imageUrl: 'https://via.placeholder.com/150' },
  { id: 2, name: 'Product 2', price: '$20', imageUrl: 'https://via.placeholder.com/150' },
  { id: 3, name: 'Product 3', price: '$30', imageUrl: 'https://via.placeholder.com/150' },
  { id: 4, name: 'Product 4', price: '$40', imageUrl: 'https://via.placeholder.com/150' },
];

function App() {
  const [cart, setCart] = useState([]);

  const handleAddToCart = (product) => {
    setCart([...cart, product]);
  };

  return (
    <div className="App">
      <h1>Product Listings</h1>
      <div className="product-list">
        {products.map((product) => (
          <ProductCard key={product.id} product={product} onAddToCart={handleAddToCart} />
        ))}
      </div>
    </div>
  );
}

export default App;
```

- **Explanation:**
  - The `App` component contains a list of products.
  - It renders a `ProductCard` for each product and passes the product details as props.
  - When the "Add to Cart" button is clicked, the `handleAddToCart` function adds the product to the `cart` state.

---

## **4. Implementing the Shopping Cart**

We will now create a cart page to show the items that the user has added to their cart.

### **Step 5: Create a Cart Component**
1. Create a new file `Cart.js` to display the shopping cart:

```jsx
import React from 'react';

function Cart({ cart, onRemoveFromCart }) {
  return (
    <div className="cart">
      <h2>Your Cart</h2>
      {cart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <ul>
          {cart.map((product, index) => (
            <li key={index}>
              {product.name} - {product.price}
              <button onClick={() => onRemoveFromCart(product)}>Remove</button>
            </li>
          ))}
        </ul>
      )}
    </div>
  );
}

export default Cart;
```

### **Step 6: Add Cart to App.js**
1. Import and use the `Cart` component in `App.js`:

```jsx
import React, { useState } from 'react';
import ProductCard from './ProductCard';
import Cart from './Cart';

const products = [
  { id: 1, name: 'Product 1', price: '$10', imageUrl: 'https://via.placeholder.com/150' },
  { id: 2, name: 'Product 2', price: '$20', imageUrl: 'https://via.placeholder.com/150' },
  { id: 3, name: 'Product 3', price: '$30', imageUrl: 'https://via.placeholder.com/150' },
  { id: 4, name: 'Product 4', price: '$40', imageUrl: 'https://via.placeholder.com/150' },
];

function App() {
  const [cart, setCart] = useState([]);

  const handleAddToCart = (product) => {
    setCart([...cart, product]);
  };

  const handleRemoveFromCart = (product) => {
    setCart(cart.filter((item) => item !== product));
  };

  return (
    <div className="App">
      <h1>Product Listings</h1>
      <div className="product-list">
        {products.map((product) => (
          <ProductCard key={product.id} product={product} onAddToCart={handleAddToCart} />
        ))}
      </div>
      <Cart cart={cart} onRemoveFromCart={handleRemoveFromCart} />
    </div>
  );
}

export default App;
```

- **Explanation:**
  - The `Cart` component takes in `cart` (the current state of the cart) and `onRemoveFromCart` (a function to remove items).
  - We added functionality to **remove items from the cart** when the "Remove" button is clicked.

---

## **5. Managing State with React Context**

To manage the cart globally (across multiple pages), we'll use the **React Context API**.

### **Step 7: Create a Cart Context**
1. Create a new file `CartContext.js`:

```jsx
import React, { createContext, useState, useContext } from 'react';

const CartContext = createContext();

export function useCart() {
  return useContext(CartContext);
}

export function CartProvider({ children }) {
  const [cart, setCart] = useState([]);

  const addToCart = (product) => setCart((prevCart) => [...prevCart, product]);
  const removeFromCart = (product) => setCart(cart.filter((item) => item !== product));

  return (
    <CartContext.Provider value={{ cart, addToCart, removeFromCart }}>
      {children}
    </CartContext.Provider>
  );
}
```

- **Explanation:**
  - We create a context for the cart and a `CartProvider` component to manage the state of the cart.

### **Step 8: Wrap Your App with the CartProvider**
1. In `index.js`, wrap the `App` component with the `CartProvider`:

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { CartProvider } from './CartContext';

ReactDOM.render(
  <CartProvider>
    <App />
  </CartProvider>,
  document.getElementById('root')
);
```

---

## **6. Adding a Checkout Page**

Now, let's add a **Checkout Page** where users can review their cart before purchasing.

### **Step 9: Create a Checkout Component**
1. Create `Checkout.js`:

```jsx
import React from 'react';
import { useCart } from './CartContext';

function Checkout() {
  const { cart } = useCart();

  return (
    <div className="checkout">
      <h2>Checkout</h2>
      {cart.length === 0 ? (
        <p>Your cart is empty</p>
      ) : (
        <div>
          <ul>
            {cart.map((product, index) => (
              <li key={index}>
                {product.name} - {product.price}
              </li>
            ))}
          </ul>
          <p>Total: ${cart.reduce((total, product) => total + parseFloat(product.price.slice(1)), 0)}</p>
        </div>
      )}
    </div>
  );
}

export default Checkout;
```

### **Step 10: Add Routing to Handle Checkout**
1. Install **React Router** for routing between pages:
   ```bash
   npm install react-router-dom
   ```

2. In `App.js`, set up the routes:

```jsx
import { BrowserRouter as Router, Route, Routes, Link } from 'react-router-dom';
import Checkout from './Checkout';

function App() {
  return (
    <Router>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/checkout">Checkout</Link>
      </nav>

      <Routes>
        <Route path="/" element={<ProductList />} />
        <Route path="/checkout" element={<Checkout />} />
      </Routes>
    </Router>
  );
}

export default App;
```

---

## **7. Styling the Shopping Site**

For this tutorial, we'll use simple **CSS** to style our shopping site.

### **Step 11: Adding CSS**
1. Create `src/App.css` with basic styles for the product list, cards, and cart.

```css
.App {
  text-align: center;
}

.product-list {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.product-card {
  border: 1px solid #ccc;
  padding: 10px;
  width: 200px;
}

.product-card img {
  width: 100%;
  height: auto;
}

.cart {
  margin-top: 20px;
}

.checkout {
  margin-top: 20px;
}
```

---

## **8. Deploying the App on Netlify**

### **Step 12: Deploy the App**

1. Push your project to **GitHub**.
2. Sign up for a **Netlify** account if you don’t have one at [Netlify](https://www.netlify.com).
3. Click on "New Site from Git" on your dashboard and select your repository.
4. In the "Build" section, set the following:
   - **Build Command**: `npm run build`
   - **Publish Directory**: `build`
5. Click "Deploy Site" to deploy your app.

Netlify will give you a live URL for your app.

---
