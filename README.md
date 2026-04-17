```markdown
# Inventory Management and Reporting System

A comprehensive full-stack inventory solution built with a secure Spring Boot REST API and a dynamic React frontend, delivering role-based access, real-time stock monitoring, and analytical insights.

---

## Table of Contents

- Features  
- Tech Stack  
- Project Structure  
- Architecture Overview  
- Database Schema  
- API Reference  
- Security & Roles  
- Getting Started  
- Configuration Reference  
- Running Tests  
- Known Issues & Notes  

---

## Features

### Backend
- **JWT Authentication**  
  Stateless authentication using JJWT (HS256) with a 24-hour token lifespan.

- **Role-Based Access Control**  
  Supports three roles: `ADMIN`, `SUPPLIER`, `CUSTOMER`.

- **Product Management**  
  Full CRUD functionality with both soft deletion (deactivation) and permanent removal.

- **Stock Handling**  
  Controlled stock updates with safeguards against invalid quantities.

- **Search & Filtering**  
  Flexible product lookup by name, category, or supplier.

- **Low-Stock Alerts**  
  Automated email notifications triggered during stock reduction or report execution.

- **Initial Data Seeding**  
  Categories and suppliers populate automatically on first launch.

- **Validation Layer**  
  Strict validation at the service level ensures data integrity.

---

### Frontend
- **Dual-panel Authentication UI**  
  Clean login and registration interface with theme switching support.

- **Dashboard**  
  Displays KPIs, category-based charts, and low-stock alerts.

- **Product Management Interface**  
  Searchable tables, inline editing, and role-based actions.

- **Product Creation Module**  
  Validated form restricted to authorized roles.

- **Reports Section**  
  Multiple analytical views computed directly from product data.

- **User Management**  
  Admin-only functionality for account creation.

- **Theme Support**  
  Persistent dark/light mode using local storage.

- **Notification System**  
  Toast-based alerts for system feedback.

- **Role Enforcement**  
  UI adapts dynamically based on user permissions.

---

## Tech Stack

### Backend
- Java 21  
- Spring Boot 3.2.5  
- Spring Security + JWT  
- Hibernate + JPA  
- MySQL 8  
- Jakarta Validation  
- Jakarta Mail  
- Lombok  
- JUnit, Mockito  
- Maven  

> Note: For Java 23 compatibility, upgrade Spring Boot to version 3.3.5.

---

### Frontend
- JavaScript (ES2022)  
- React 18  
- React Router v6  
- Axios  
- Custom Canvas Charts  
- CSS (with variables and theming)  
- Create React App  

---

##Project Structure
```
.
├── backend/                          # Spring Boot application
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/
│   │   │   │   ├── database/
│   │   │   │   │   └── DatabaseConnection.java       # Legacy JDBC utility (unused by JPA layer)
│   │   │   │   └── inventory/
│   │   │   │       ├── ServerApplication.java         # Entry point; @EntityScan / @EnableJpaRepositories
│   │   │   │       ├── config/
│   │   │   │       │   └── StarterDataConfig.java     # Seeds categories & suppliers on first start
│   │   │   │       ├── controller/
│   │   │   │       │   ├── Controller.java            # /api/** — products, stock, reports
│   │   │   │       │   └── AuthController.java        # /auth/register, /auth/login
│   │   │   │       ├── authservice/
│   │   │   │       │   ├── AuthService.java           # Register & login business logic
│   │   │   │       │   └── CustomUserDetailsService.java  # Loads User by username for Spring Security
│   │   │   │       ├── security/
│   │   │   │       │   ├── WebSecurityConfig.java     # Filter chain, route permissions
│   │   │   │       │   ├── SecurityBeansConfig.java   # PasswordEncoder, AuthProvider, AuthManager
│   │   │   │       │   ├── JwtAuthenticationFilter.java  # Extracts & validates JWT on every request
│   │   │   │       │   └── CorsConfig.java            # CORS: allows http://localhost:3000
│   │   │   │       ├── utils/
│   │   │   │       │   └── AuthUtil.java              # JWT generate / validate / extract claims
│   │   │   │       ├── entity/
│   │   │   │       │   ├── User.java                  # Spring Security UserDetails + Lombok @Builder
│   │   │   │       │   └── Role.java                  # Enum: ADMIN, SUPPLIER, CUSTOMER
│   │   │   │       ├── repository/
│   │   │   │       │   └── UserRepository.java        # JPA repo for users table
│   │   │   │       ├── dto/
│   │   │   │       │   ├── AuthRequest.java           # { username, password }
│   │   │   │       │   ├── AuthResponse.java          # { token, username, role }
│   │   │   │       │   └── RegisterRequest.java       # { username, password, email, role }
│   │   │   │       ├── database_system/
│   │   │   │       │   ├── entity/
│   │   │   │       │   │   ├── Product.java           # Core inventory entity
│   │   │   │       │   │   ├── Category.java          # Product category
│   │   │   │       │   │   ├── Supplier.java          # Supplier contact info
│   │   │   │       │   │   └── Transaction.java       # Stock movement history record
│   │   │   │       │   └── repository/
│   │   │   │       │       ├── ProductRepository.java # Derived-query JPA methods
│   │   │   │       │       ├── CategoryRepository.java
│   │   │   │       │       └── SupplierRepository.java
│   │   │   │       ├── dao/
│   │   │   │       │   └── ProductDAO.java            # Repository wrapper (add, update, soft/hard delete, search)
│   │   │   │       ├── service/
│   │   │   │       │   ├── ProductService.java        # Delegates to ProductDAO after validation
│   │   │   │       │   ├── InventoryService.java      # Stock increase/reduce; triggers low-stock check
│   │   │   │       │   └── validation/
│   │   │   │       │       ├── ProductValidator.java  # Validates name, SKU, price, category, supplier
│   │   │   │       │       └── InventoryValidator.java # Validates qty > 0, stock >= requested qty
│   │   │   │       └── Report/
│   │   │   │           ├── InventoryReportService.java # Detects low-stock; formats & sends alert email
│   │   │   │           └── EmailService.java           # SMTP send via Gmail App Password
│   │   │   └── resources/
│   │   │       ├── application.properties             # DB, JPA, JWT config
│   │   │       └── stockmanagement.sql                # Legacy SQL seed file (56 sample products)
│   │   └── test/java/com/inventory/
│   │       ├── ControllerTest.java
│   │       ├── InventoryServiceTest.java
│   │       ├── InventoryValidatorTest.java
│   │       ├── ProductDAOTest.java
│   │       ├── ProductEntityTest.java
│   │       ├── ProductServiceTest.java
│   │       ├── ProductValidatorTest.java
│   │       ├── ServerApplicationTests.java
│   │       └── security/
│   │           ├── AuthControllerTest.java
│   │           ├── AuthServiceTest.java
│   │           ├── AuthUtilTest.java
│   │           ├── CustomUserDetailsServiceTest.java
│   │           └── JwtAuthenticationFilterTest.java
│   └── pom.xml
│
└── frontend/                         # React application
├── public/
│   └── index.html
├── src/
│   ├── App.js                    # Router, context providers, Protected route wrapper
│   ├── pages/
│   │   ├── LoginPage.jsx         # Split-panel login with theme toggle
│   │   ├── RegisterPage.jsx      # Registration form with role selector
│   │   ├── DashboardPage.jsx     # Summary cards, category chart, low-stock list
│   │   ├── ProductsPage.jsx      # Searchable product table with edit/delete
│   │   ├── AddProductPage.jsx    # Add-product form (SUPPLIER+)
│   │   ├── ReportsPage.jsx       # 4-tab analytics: Summary/Low Stock/Value/Categories
│   │   └── AddUserPage.jsx       # Create user form (ADMIN only)
│   ├── components/
│   │   ├── Navbar.jsx            # Fixed top bar: title, theme toggle, notifications, user badge
│   │   ├── Sidebar.jsx           # Fixed left nav with role-filtered links
│   │   ├── ProductForm.jsx       # Reusable add/edit form with client-side validation
│   │   ├── ProductTable.jsx      # Data table with status badges and action buttons
│   │   ├── ChartComponent.jsx    # Canvas bar chart (no external library)
│   │   ├── SummaryCard.jsx       # Animated KPI card with hover lift effect
│   │   └── LowStockBadge.jsx     # "LOW STOCK" / "OK" inline status badge
│   ├── context/
│   │   ├── AuthContext.js        # Login/logout state, role hierarchy, token persistence
│   │   ├── InventoryContext.jsx  # Global product state (fetch, add, update, delete)
│   │   ├── ThemeContext.js       # dark/light toggle, persisted to localStorage
│   │   └── ToastContext.jsx      # Stacked toast notifications (success/error/warning/info)
│   ├── hooks/
│   │   ├── useAuth.js            # Thin wrapper around AuthContext
│   │   ├── useProducts.js        # Fetch, normalise, add, edit, delete; lowStockProducts memo
│   │   └── useReports.js         # Report fetching hook (currently unused)
│   ├── services/
│   │   ├── AuthService.js        # register / login / logout helpers
│   │   ├── ProductService.js     # REST calls for products
│   │   └── UserService.js        # REST calls for /api/users (ADMIN feature)
│   ├── utilities/
│   │   ├── ApiUtils.js           # Axios instance with JWT interceptor
│   │   ├── Constants.js          # API_BASE_URL, ROLES, TOKEN_KEY, CATEGORIES
│   │   ├── StorageUtils.js       # localStorage helpers for token and user object
│   │   ├── ValidationUtils.js    # validateProduct / validateLogin / validateRegister
│   │   └── FormatUtils.js        # Indian-locale currency (INR), number formatting, truncate
│   └── styles/
│       ├── global.css            # CSS variables (dark/light themes), base styles, buttons, cards
│       ├── dashboard.css         # Dashboard grid, summary cards, chart panels
│       ├── inventory.css         # Table, form, toolbar, badge styles
│       └── login.css             # Split-panel login layout
└── package.json
```
## Architecture Overview

```
React Frontend (Browser)
      |
      |  HTTP Requests (JWT Auth)
      v
Spring Boot Backend
      |
      |  JPA / Hibernate
      v
MySQL Database
```

Core backend responsibilities include:
- Token validation
- Request authorization
- Business logic execution
- Data persistence

---

## Database Schema

### users
Stores authentication and role data.

### products
Core inventory entity with stock tracking and metadata.

### categories
Product classification (auto-seeded on first launch).

### supplier
Supplier contact information.

### transactions
Logs stock movement history.

> Tables are generated automatically via Hibernate.

---

## API Reference

### Authentication
- `POST /auth/register`
- `POST /auth/login`

### Products
- Retrieve, search, filter, add, update, delete, deactivate.

### Stock
- Increase or reduce product quantities.

### Reports
- Generate low-stock alerts via email.

---

## Security & Roles

### JWT Workflow
1. User logs in → receives token  
2. Token stored on client  
3. Sent with each request  
4. Backend validates and authorizes  

### Role Capabilities

| Action | CUSTOMER | SUPPLIER | ADMIN |
|--------|----------|----------|-------|
| View Data | ✓ | ✓ | ✓ |
| Modify Products | ✗ | ✓ | ✓ |
| Delete Products | ✗ | ✗ | ✓ |
| Manage Users | ✗ | ✗ | ✓ |

---

## Getting Started

### Prerequisites
- Java 21  
- Maven  
- Node.js  
- MySQL  
- Gmail account (for SMTP alerts)

---

### Database Setup
```sql
CREATE DATABASE stockmanagement;
```

---

### Email Configuration
- Enable Gmail 2FA
- Generate an App Password
- Insert credentials into `EmailService`

---

### Backend Setup

```bash
cd backend
./mvnw spring-boot:run
```

Runs on:
```
http://localhost:8080
```

---

### Frontend Setup

```bash
cd frontend
npm install
npm start
```

Runs on:
```
http://localhost:3000
```

---

## Configuration Reference

Key properties include:
- Database connection settings
- Hibernate behavior
- JWT secret and expiration

> Always replace the default JWT secret before deploying to production.

---

## Running Tests

```bash
./mvnw test
```

Includes:
- Controller tests
- Service tests
- Security tests
- Validation tests

---

## Known Issues & Notes

- **Java 23 Compatibility**  
  Requires a Spring Boot upgrade due to Mockito limitations.

- **Backend Authorization**  
  Role enforcement is primarily handled on the frontend side.

- **CORS Restriction**  
  Default configuration allows only `localhost:3000`.

- **Email Setup Required**  
  No default recipient address is configured out of the box.

- **Legacy Code Present**  
  Some unused classes and SQL files are retained for reference purposes.

- **Static Dropdown Values**  
  Category and supplier IDs are currently hardcoded in the frontend.
```