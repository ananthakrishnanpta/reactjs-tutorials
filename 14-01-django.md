# **Django + ReactJS Online Shopping App with Full Features**

This tutorial will walk you through building a **complete e-commerce application** using **Django** for the backend, **ReactJS** for the frontend, and **Razorpay** for payment integration.

---

## **1. Django Setup**

### **1.1 Create a Virtual Environment**

Start by creating and activating a virtual environment:

```bash
mkdir django-react-shop
cd django-react-shop
python -m venv venv
source venv/bin/activate  # For Windows: venv\Scripts\activate
```

### **1.2 Install Dependencies**

Install Django, Django REST Framework, Razorpay, JWT, and CORS headers:

```bash
pip install django djangorestframework djangorestframework-simplejwt razorpay django-cors-headers
```

### **1.3 Create Django Project**

Create the Django project and app:

```bash
django-admin startproject backend
cd backend
python manage.py startapp store
```

### **1.4 Configure `settings.py`**

In `backend/settings.py`, add the installed apps and configure CORS and JWT authentication:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'rest_framework_simplejwt',
    'store',
    'corsheaders',  # Allow CORS for React
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # Enable CORS
    'django.middleware.common.CommonMiddleware',
    # other middleware...
]

# CORS settings to allow React app to connect with Django API
CORS_ORIGIN_WHITELIST = [
    'http://localhost:3000',  # React frontend
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}
```

---

## **2. Define Models in Django**

Create models for `Product`, `Cart`, and `Order` in `store/models.py`:

```python
from django.db import models
from django.contrib.auth.models import User

# Product Model
class Product(models.Model):
    name = models.CharField(max_length=255)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    image = models.ImageField(upload_to='products/')
    
    def __str__(self):
        return self.name

# Cart Model
class Cart(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    quantity = models.PositiveIntegerField()

# Order Model
class Order(models.Model):
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    total_amount = models.DecimalField(max_digits=10, decimal_places=2)
    shipping_address = models.TextField()
    status = models.CharField(max_length=100, default="Pending")
    created_at = models.DateTimeField(auto_now_add=True)
```

### **2.1 Create Migrations**

Run the following to create database tables:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## **3. Create API Views and Serializers**

### **3.1 Create Serializers**

In `store/serializers.py`, create serializers for the models:

```python
from rest_framework import serializers
from .models import Product, Cart, Order

# Serializer for Product Model
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = '__all__'

# Serializer for Cart Model
class CartSerializer(serializers.ModelSerializer):
    class Meta:
        model = Cart
        fields = '__all__'

# Serializer for Order Model
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = Order
        fields = '__all__'
```

### **3.2 Create Views**

In `store/views.py`, create API views for products, carts, orders, and authentication:

```python
from rest_framework import generics
from .models import Product, Cart, Order
from .serializers import ProductSerializer, CartSerializer, OrderSerializer
from rest_framework.permissions import IsAuthenticated
from rest_framework_simplejwt.tokens import RefreshToken
from django.contrib.auth.models import User

# Product List View
class ProductListView(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

# Cart List and Create View
class CartListView(generics.ListCreateAPIView):
    queryset = Cart.objects.all()
    serializer_class = CartSerializer
    permission_classes = [IsAuthenticated]

# Order Create View
class OrderCreateView(generics.CreateAPIView):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer
    permission_classes = [IsAuthenticated]

# Login View to generate JWT
class LoginView(generics.GenericAPIView):
    def post(self, request):
        username = request.data['username']
        password = request.data['password']
        
        user = User.objects.filter(username=username).first()
        if user and user.check_password(password):
            refresh = RefreshToken.for_user(user)
            return Response({'access': str(refresh.access_token)}, status=200)
        return Response({'error': 'Invalid credentials'}, status=400)
```

### **3.3 URLs Configuration**

In `store/urls.py`, define the URLs for the API:

```python
from django.urls import path
from .views import ProductListView, CartListView, OrderCreateView, LoginView

urlpatterns = [
    path('products/', ProductListView.as_view(), name='product-list'),
    path('cart/', CartListView.as_view(), name='cart-list'),
    path('order/', OrderCreateView.as_view(), name='order-create'),
    path('login/', LoginView.as_view(), name='login'),
]
```

In `backend/urls.py`, include the app URLs:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('store.urls')),
]
```

---

## **4. Razorpay Integration**

### **4.1 Configure Razorpay**

In `backend/settings.py`, add your Razorpay credentials:

```python
RAZORPAY_API_KEY = 'your_razorpay_api_key'
RAZORPAY_API_SECRET = 'your_razorpay_api_secret'
```

### **4.2 Razorpay API for Order Creation**

In `store/views.py`, add an endpoint to create Razorpay orders:

```python
import razorpay
from django.conf import settings
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status

class RazorpayOrderView(APIView):
    def post(self, request):
        amount = request.data['amount']  # Amount in paisa (100 INR = 10000 paisa)
        client = razorpay.Client(auth=(settings.RAZORPAY_API_KEY, settings.RAZORPAY_API_SECRET))
        
        try:
            order = client.order.create({
                'amount': amount,
                'currency': 'INR',
                'payment_capture': '1',
            })
            return Response({'orderId': order['id'], 'amount': amount}, status=status.HTTP_200_OK)
        except Exception as e:
            return Response({'error': str(e)}, status=status.HTTP_400_BAD_REQUEST)
```

---

## **5. Frontend Setup (ReactJS)**

### **5.1 Create React App**

Create the React app:

```bash
npx create-react-app frontend
cd frontend
npm start
```

### **5.2 Install Axios**

```bash
npm install axios
```

### **5.3 ProductList Component**

Create a `ProductList` component to fetch products from the backend and display them:

```javascript
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:8000/api/products/')
      .then(response => {
        setProducts(response.data);
      })
      .catch(error => {
        console.error('Error fetching products:', error);
      });
  }, []);

  return (
    <div>
      {products.map(product => (
        <div key={product.id}>
          <h2>{product.name}</h2>
          <p>{product.description}</p>
          <p>₹{product.price}</p>
        </div>
      ))}
    </div>
  );
}

export default ProductList;
```

### **5.4 Cart Component**

Create a `Cart` component that allows users to add products to their cart:

```javascript
import React, { useState } from 'react';
import axios from 'axios';

function Cart() {
  const [cart, setCart] = useState([]);
  const [quantity, setQuantity] = useState(1);

  const addToCart = (productId) => {
    axios.post('http://localhost:8000/api/cart/', { product: productId, quantity })
      .then(response => {
        setCart([...cart, response.data]);
      })
      .catch(error => {
        console.error('Error adding product to cart:', error);
      });
  };

  return (
    <div>
      <button onClick={() => addToCart(1)}>Add to Cart</button> {/* Example product ID */}
    </div>
  );
}

export default Cart;
```

### **5.5 Payment Component (Razorpay Integration)**

Integrate Razorpay in the frontend to handle payments:

```javascript
import React from 'react';

function Payment({ amount, orderId }) {
  const handlePayment = () => {
    var options = {
      key: 'your_razorpay_key_id', // Your Razorpay Key ID
      amount: amount, // Amount to be paid (in paisa)
      currency: 'INR',
      name: 'Online Shopping',
      order_id: orderId,
      handler: function (response) {
        alert('Payment successful: ' + response.razorpay_payment_id);
      },
      prefill: {
        name: 'Customer',
        email: 'customer@example.com',
        contact: '1234567890',
      },
      theme: {
        color: '#F37254',
      },
    };

    var rzp1 = new Razorpay(options);
    rzp1.open();
  };

  return (
    <button onClick={handlePayment}>Pay Now</button>
  );
}

export default Payment;
```

---

## **6. Deployment**

### **6.1 Backend Deployment (Heroku)**

1. Create a **Heroku account** and install the **Heroku CLI**.
2. In your Django project, create a `Procfile`:

   ```
   web: gunicorn backend.wsgi --log-file -
   ```

3. Install `gunicorn`:

   ```bash
   pip install gunicorn
   ```

4. Push your code to **GitHub** and deploy it on **Heroku** using Heroku's GitHub integration.

### **6.2 Frontend Deployment (Netlify)**

1. Build the React app:

   ```bash
   npm run build
   ```

2. Deploy the build folder on **Netlify**:
   - Create a **Netlify account** and connect it to your **GitHub repository

**.
   - Set up your **React app** deployment with automatic deployments from GitHub.

---
