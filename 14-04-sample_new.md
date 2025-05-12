# React + Vite Frontend with Django + DRF Backend

## Project Folder Structure

```plaintext
project-folder/
├── backend/             # Django Backend
│   ├── manage.py
│   ├── backend/         # Django project files
│   ├── products/        # Django app for managing products
│   ├── db.sqlite3
├── frontend/            # React Frontend
│   ├── vite.config.js
│   ├── src/
│   ├── public/
│   ├── node_modules/
└── venv/                # Python virtual environment (optional)
```

---

## 1. Setting Up the Django Backend

### Step 1: Install Django and DRF

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install django djangorestframework django-cors-headers
```

### Step 2: Create Django Project and App

```bash
django-admin startproject backend
cd backend
python manage.py startapp products
```

### Step 3: Configure the Project

* **Add to `INSTALLED_APPS` in `backend/settings.py`**:

  ```python
  INSTALLED_APPS = [
      ...
      'corsheaders',
      'rest_framework',
      'products',
  ]
  ```

* **Add CORS Middleware**:

  ```python
  MIDDLEWARE = [
      ...
      'corsheaders.middleware.CorsMiddleware',
      'django.middleware.common.CommonMiddleware',
  ]
  ```

* **CORS Allowed Origins**:

  ```python
  CORS_ALLOWED_ORIGINS = [
      "http://localhost:5173",  # Vite default port
  ]
  ```

### Step 4: Create Product Model

In `products/models.py`:

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    image = models.URLField()  # URL for product image

    def __str__(self):
        return self.name
```

Run migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Step 5: Create Serializer and API View

* **`products/serializers.py`**:

  ```python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    image = serializers.SerializerMethodField()

    def get_image(self, obj):
        request = self.context.get('request')
        if obj.image:
            return request.build_absolute_uri(obj.image.url)
        return None

    class Meta:
        model = Product
        fields = '__all__'

  ```

* **`products/views.py`**:

  ```python
from rest_framework.response import Response
from rest_framework.views import APIView
from .models import Product
from .serializers import ProductSerializer

class ProductListView(APIView):
    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True, context={'request': request})
        return Response(serializer.data)

  ```

* **`products/urls.py`**:

  ```python
  from django.urls import path
  from .views import ProductListView

  urlpatterns = [
      path('products/', ProductListView.as_view(), name='product-list'),
  ]
  ```

* **Include in `backend/urls.py`**:

  ```python
  from django.contrib import admin
  from django.urls import path, include

  urlpatterns = [
      path('admin/', admin.site.urls),
      path('api/', include('products.urls')),
  ]
  ```

### Step 6: Add Sample Data

Run the server:

```bash
python manage.py runserver
```

Navigate to `http://127.0.0.1:8000/admin` to add products via the admin interface.

---

## 2. Setting Up React Frontend with Vite

### Step 1: Create Vite Project

```bash
npm create vite@latest frontend --template react
cd frontend
npm install
npm install axios bootstrap
```

### Step 2: Import Bootstrap

In `src/main.jsx`:

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import 'bootstrap/dist/css/bootstrap.min.css';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

### Step 3: Create ProductCard Component

`src/components/ProductCard.jsx`:

```jsx
import React from 'react';

const ProductCard = ({ product }) => {
  return (
    <div className="col-md-4">
      <div className="card">
        <img src={product.image} className="card-img-top" alt={product.name} />
        <div className="card-body">
          <h5 className="card-title">{product.name}</h5>
          <p className="card-text">${product.price}</p>
        </div>
      </div>
    </div>
  );
};

export default ProductCard;
```

### Step 4: Create Home Page

`src/pages/Home.jsx`:

```jsx
import React, { useEffect, useState } from 'react';
import axios from 'axios';
import ProductCard from '../components/ProductCard';

const Home = () => {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    axios.get('http://127.0.0.1:8000/api/products/')
      .then((response) => {
        setProducts(response.data);
      })
      .catch((error) => {
        console.error('Error fetching products:', error);
      });
  }, []);

  return (
    <div className="container mt-5">
      <h1>Products</h1>
      <div className="row">
        {products.map((product) => (
          <ProductCard key={product.id} product={product} />
        ))}
      </div>
    </div>
  );
};

export default Home;
```

### Step 5: Update App Component

`src/App.jsx`:

```jsx
import React from 'react';
import Home from './pages/Home';

const App = () => {
  return <Home />;
};

export default App;
```

### Step 6: Update Vite Configuration

In `vite.config.js`:

```javascript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
  },
});
```

### Step 7: Run the React App

Run the development server:

```bash
npm run dev
```

Navigate to `http://localhost:5173` to see the products displayed.

---

## 3. Running the Project

1. Start the **Django server**:

   ```bash
   python manage.py runserver
   ```
2. Start the **React server**:

   ```bash
   npm run dev
   ```

Visit `http://localhost:5173` to view your application.

---

## Troubleshooting

* **CORS Errors**: Ensure `CORS_ALLOWED_ORIGINS` includes your React app's URL.
* **API Issues**: Verify the Django server is running and accessible at `http://127.0.0.1:8000/api/products/`.
* **Static Images**: Ensure URLs in the database are correct.

---
