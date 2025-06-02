# **React + Vite Frontend with Django + DRF Backend: JWT Authentication**

This tutorial builds on the original setup, introducing **JWT authentication** to secure the API and integrate authentication features into the frontend.

---

## **Project Folder Structure**

```plaintext
project-folder/
├── backend/             # Django Backend
│   ├── manage.py
│   ├── backend/         # Django project files
│   ├── products/        # Django app for managing products
│   ├── users/           # Django app for user management
│   ├── db.sqlite3
├── frontend/            # React Frontend
│   ├── vite.config.js
│   ├── src/
│   ├── public/
│   ├── node_modules/
└── venv/                # Python virtual environment (optional)
```

---

## **1. Setting Up JWT in Django Backend**

### **Step 1: Install Required Packages**

Install Django REST framework JWT and djangorestframework-simplejwt:

```bash
pip install djangorestframework-simplejwt
```

### **Step 2: Configure DRF Settings**

Update `backend/settings.py`:

```python
from datetime import timedelta

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=5),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
    'ROTATE_REFRESH_TOKENS': True,
    'BLACKLIST_AFTER_ROTATION': True,
}
```

### **Step 3: Create a `users` App**

```bash
python manage.py startapp users
```

Add `users` to `INSTALLED_APPS` in `settings.py`:

```python
INSTALLED_APPS = [
    ...
    'users',
]
```

### **Step 4: Create a Custom User Serializer**

In `users/serializers.py`:

```python
from django.contrib.auth.models import User
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email')
```

### **Step 5: Add JWT Views**

In `users/urls.py`:

```python
from django.urls import path
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('login/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

Include in `backend/urls.py`:

```python
from django.urls import include

urlpatterns += [
    path('api/users/', include('users.urls')),
]
```

### **Step 6: Protect the Product API**

In `products/views.py`:

```python
from rest_framework.permissions import IsAuthenticated
from rest_framework.views import APIView
from .models import Product
from .serializers import ProductSerializer

class ProductListView(APIView):
    permission_classes = [IsAuthenticated]

    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True, context={'request': request})
        return Response(serializer.data)
```

---

## **2. Implementing JWT Authentication in React Frontend**

### **Step 1: Install JWT Libraries**

```bash
npm install jwt-decode
```

### **Step 2: Create an Authentication Context**

Create `src/context/AuthContext.jsx`:

```jsx
import React, { createContext, useState } from 'react';
import jwtDecode from 'jwt-decode';
import axios from 'axios';

export const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
    const [user, setUser] = useState(() => {
        const token = localStorage.getItem('access_token');
        return token ? jwtDecode(token) : null;
    });

    const login = async (username, password) => {
        const response = await axios.post('http://127.0.0.1:8000/api/users/login/', { username, password });
        const { access, refresh } = response.data;

        localStorage.setItem('access_token', access);
        localStorage.setItem('refresh_token', refresh);
        setUser(jwtDecode(access));
    };

    const logout = () => {
        localStorage.removeItem('access_token');
        localStorage.removeItem('refresh_token');
        setUser(null);
    };

    return (
        <AuthContext.Provider value={{ user, login, logout }}>
            {children}
        </AuthContext.Provider>
    );
};
```

Wrap your app in `AuthProvider` in `src/main.jsx`:

```jsx
import { AuthProvider } from './context/AuthContext';

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <AuthProvider>
            <App />
        </AuthProvider>
    </React.StrictMode>
);
```

### **Step 3: Create Login Page**

Create `src/pages/Login.jsx`:

```jsx
import React, { useContext, useState } from 'react';
import { AuthContext } from '../context/AuthContext';

const Login = () => {
    const { login } = useContext(AuthContext);
    const [username, setUsername] = useState('');
    const [password, setPassword] = useState('');

    const handleSubmit = async (e) => {
        e.preventDefault();
        try {
            await login(username, password);
            alert('Login successful!');
        } catch (error) {
            console.error('Login failed:', error);
            alert('Invalid credentials');
        }
    };

    return (
        <div className="container mt-5">
            <form onSubmit={handleSubmit}>
                <h1>Login</h1>
                <div className="mb-3">
                    <label>Username</label>
                    <input
                        type="text"
                        className="form-control"
                        value={username}
                        onChange={(e) => setUsername(e.target.value)}
                    />
                </div>
                <div className="mb-3">
                    <label>Password</label>
                    <input
                        type="password"
                        className="form-control"
                        value={password}
                        onChange={(e) => setPassword(e.target.value)}
                    />
                </div>
                <button type="submit" className="btn btn-primary">Login</button>
            </form>
        </div>
    );
};

export default Login;
```

### **Step 4: Secure API Requests**

Use Axios interceptors in `src/api.js`:

```javascript
import axios from 'axios';

const api = axios.create({
    baseURL: 'http://127.0.0.1:8000/api/',
});

api.interceptors.request.use((config) => {
    const token = localStorage.getItem('access_token');
    if (token) {
        config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
});

export default api;
```

---

## **3. Authorization and Logout**

* Use `AuthContext` to check if a user is authenticated and conditionally render components.
* Implement a logout button to clear the token.

---
