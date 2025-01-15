# **ReactJS + Spring Boot Online Shopping App**

This project will use **Spring Boot** for the backend (RESTful APIs), **ReactJS** for the frontend, **Razorpay** for payment integration, and **JWT** for authentication.

---

## **1. Spring Boot Setup**

### **1.1 Create a Spring Boot Project**

Use **Spring Initializr** to generate a Spring Boot project:

1. Go to [Spring Initializr](https://start.spring.io/).
2. Select **Maven Project** and **Java**.
3. Add dependencies:
   - **Spring Web**
   - **Spring Data JPA**
   - **Spring Security**
   - **MySQL Driver** (or **H2** for development)
   - **JWT (JSON Web Token)** for authentication
   - **Razorpay SDK** (for payment)
4. Click **Generate** to download the project.

### **1.2 Directory Structure**

The basic directory structure will look like this:

```
my-shop/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   ├── com/
│   │   │   │   ├── example/
│   │   │   │   │   ├── shopping/
│   │   │   │   │   │   ├── controller/
│   │   │   │   │   │   ├── model/
│   │   │   │   │   │   ├── repository/
│   │   │   │   │   │   ├── service/
│   │   │   │   │   │   ├── config/
│   │   │   │   │   │   ├── util/
│   │   │   │   │   │   ├── exception/
│   │   │   │   │   │   └── jwt/
│   ├── resources/
│   │   ├── application.properties
│   │   ├── static/
│   │   └── templates/
└── pom.xml
```

---

## **2. Spring Boot Configuration**

### **2.1 Dependencies in `pom.xml`**

Ensure the following dependencies are added in your `pom.xml`:

```xml
<dependencies>
    <!-- Spring Web for creating RESTful APIs -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    
    <!-- Spring Security for Authentication & Authorization -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    
    <!-- Spring Data JPA for database interaction -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    
    <!-- JWT for token-based authentication -->
    <dependency>
        <groupId>io.jsonwebtoken</groupId>
        <artifactId>jjwt</artifactId>
        <version>0.11.5</version>
    </dependency>
    
    <!-- Razorpay SDK -->
    <dependency>
        <groupId>com.razorpay</groupId>
        <artifactId>razorpay-java</artifactId>
        <version>2.0.0</version>
    </dependency>
    
    <!-- MySQL Driver -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    
    <!-- Spring Boot DevTools for automatic restarts -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### **2.2 Application Configuration**

In the `src/main/resources/application.properties`, configure the database connection and JWT settings:

```properties
# Database Configuration (use MySQL or H2 for development)
spring.datasource.url=jdbc:mysql://localhost:3306/shopping_db
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# JWT Configuration
jwt.secret=your_jwt_secret_key
jwt.expiration=86400000  # 1 Day
```

### **2.3 Models**

Define the **Product**, **Cart**, **Order**, and **User** models:

```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String description;
    private BigDecimal price;
    private String image;

    // Getters and Setters
}

@Entity
public class Cart {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    private User user;

    @ManyToOne
    private Product product;

    private int quantity;

    // Getters and Setters
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    private User user;

    private BigDecimal totalAmount;
    private String shippingAddress;

    // Getters and Setters
}

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;
    private String password;

    // Getters and Setters
}
```

### **2.4 Repositories**

Create repositories for each model in `repository` package:

```java
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {}

@Repository
public interface CartRepository extends JpaRepository<Cart, Long> {}

@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {}

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

---

## **3. Security & Authentication**

### **3.1 JWT Authentication**

Create classes for JWT token generation and validation. First, create the utility class for generating and parsing JWT:

```java
@Component
public class JwtUtil {
    private String secretKey = "your_jwt_secret_key";

    public String generateToken(User user) {
        return Jwts.builder()
            .setSubject(user.getUsername())
            .setExpiration(new Date(System.currentTimeMillis() + 86400000)) // 1 day
            .signWith(SignatureAlgorithm.HS256, secretKey)
            .compact();
    }

    public String extractUsername(String token) {
        return Jwts.parser()
            .setSigningKey(secretKey)
            .parseClaimsJws(token)
            .getBody()
            .getSubject();
    }

    public boolean isTokenExpired(String token) {
        return extractExpiration(token).before(new Date());
    }

    private Date extractExpiration(String token) {
        return Jwts.parser()
            .setSigningKey(secretKey)
            .parseClaimsJws(token)
            .getBody()
            .getExpiration();
    }
}
```

### **3.2 Spring Security Configuration**

Configure Spring Security to enable JWT-based authentication:

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    private JwtUtil jwtUtil;

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .authorizeRequests()
            .antMatchers("/login", "/register").permitAll()
            .anyRequest().authenticated()
            .and().addFilter(new JwtFilter(authenticationManager(), jwtUtil));
    }
}
```

### **3.3 Login & Registration Endpoint**

Create endpoints for login and registration in `controller`:

```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private JwtUtil jwtUtil;

    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody User user) {
        Optional<User> foundUser = userRepository.findByUsername(user.getUsername());
        if (foundUser.isPresent() && passwordEncoder.matches(user.getPassword(), foundUser.get().getPassword())) {
            String token = jwtUtil.generateToken(foundUser.get());
            return ResponseEntity.ok(new AuthenticationResponse(token));
        } else {
            return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid Credentials");
        }
    }
}
```

---

## **4. Razorpay Integration**

### **4.1 Payment Controller**

In `controller/PaymentController.java`, create a Razorpay order:

```java
@RestController
@RequestMapping("/payment")
public class PaymentController {

    private RazorpayClient razorpayClient;

    @Autowired
    public PaymentController() {
        this.razorpayClient = new RazorpayClient("your_razorpay_api_key", "your_razorpay_api_secret");
    }

    @PostMapping("/create-order")
    public ResponseEntity<?> createOrder(@RequestBody OrderRequest orderRequest) {
        try {
            JSONObject orderRequestJson = new JSONObject();
            orderRequestJson.put("amount", orderRequest.getAmount() * 100); // amount in paisa
            orderRequestJson.put("currency", "INR");
            orderRequestJson.put("payment_capture", 1);

            Order order = razorpayClient.orders.create(orderRequestJson);
            return ResponseEntity.ok(order.toString());
        } catch (RazorpayException e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Payment initiation failed");
        }
    }
}
```

---

## **5. Frontend Setup (ReactJS)**

### **5.1 Create React App**

Create the React app using:

```bash
npx create-react-app frontend
cd frontend
npm install axios
npm start
```

### **5.2 Display Products**

Create a `ProductList.js` component:

```javascript
import React, { useEffect, useState } from 'react';
import axios from 'axios';

function ProductList() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:8080/products')
      .then(response => setProducts(response.data))
      .catch(error => console.error(error));
  }, []);

  return (
    <div>
      {products.map(product => (
        <div key={product.id}>
          <h

2>{product.name}</h2>
          <p>{product.description}</p>
          <p>{product.price}</p>
        </div>
      ))}
    </div>
  );
}

export default ProductList;
```

### **5.3 Payment Integration**

Use Razorpay's JavaScript SDK to handle payments:

```html
<script src="https://checkout.razorpay.com/v1/checkout.js"></script>
```

### **5.4 Cart and Order Management**

Set up **React Context** or **useState** to manage the cart and handle order submission.

---

## **6. Deployment on Free Services**

### **6.1 Deploying Spring Boot on Heroku**

1. Create a Heroku account and install the Heroku CLI.
2. Deploy the Spring Boot application:
   ```bash
   heroku create
   git push heroku master
   ```

### **6.2 Deploying React on Netlify**

1. Create a GitHub repo for the frontend.
2. Connect the repo to **Netlify** for deployment:
   - Go to Netlify and click **New Site from Git**.
   - Select your GitHub repo and deploy it.

--- 
