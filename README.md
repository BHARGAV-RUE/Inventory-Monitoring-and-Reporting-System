# Inventory Management and Reporting System

A comprehensive full-stack inventory solution built with a secure Spring Boot REST API and a dynamic React frontend, delivering role-based access, real-time stock monitoring, and analytical insights.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Architecture Overview](#architecture-overview)
- [Database Schema](#database-schema)
- [API Reference](#api-reference)
- [Security & Roles](#security--roles)
- [Getting Started](#getting-started)
- [Configuration Reference](#configuration-reference)
- [Running Tests](#running-tests)
- [Known Issues & Notes](#known-issues--notes)

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

### Frontend
- JavaScript (ES2022)
- React 18
- React Router v6
- Axios
- Custom Canvas Charts
- CSS (with variables and theming)
- Create React App

---

## Project Structure

```
.
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/
│   │   │   │   ├── database/
│   │   │   │   │   └── DatabaseConnection.java
│   │   │   │   └── inventory/
│   │   │   │       ├── ServerApplication.java
│   │   │   │       ├── config/
│   │   │   │       │   └── StarterDataConfig.java
│   │   │   │       ├── controller/
│   │   │   │       │   ├── Controller.java
│   │   │   │       │   └── AuthController.java
│   │   │   │       ├── authservice/
│   │   │   │       │   ├── AuthService.java
│   │   │   │       │   └── CustomUserDetailsService.java
│   │   │   │       ├── security/
│   │   │   │       │   ├── WebSecurityConfig.java
│   │   │   │       │   ├── SecurityBeansConfig.java
│   │   │   │       │   ├── JwtAuthenticationFilter.java
│   │   │   │       │   └── CorsConfig.java
│   │   │   │       ├── utils/
│   │   │   │       │   └── AuthUtil.java
│   │   │   │       ├── entity/
│   │   │   │       │   ├── User.java
│   │   │   │       │   └── Role.java
│   │   │   │       ├── repository/
│   │   │   │       │   └── UserRepository.java
│   │   │   │       ├── dto/
│   │   │   │       │   ├── AuthRequest.java
│   │   │   │       │   ├── AuthResponse.java
│   │   │   │       │   └── RegisterRequest.java
│   │   │   │       ├── database_system/
│   │   │   │       │   ├── entity/
│   │   │   │       │   │   ├── Product.java
│   │   │   │       │   │   ├── Category.java
│   │   │   │       │   │   ├── Supplier.java
│   │   │   │       │   │   └── Transaction.java
│   │   │   │       │   └── repository/
│   │   │   │       │       ├── ProductRepository.java
│   │   │   │       │       ├── CategoryRepository.java
│   │   │   │       │       └── SupplierRepository.java
│   │   │   │       ├── dao/
│   │   │   │       │   └── ProductDAO.java
│   │   │   │       ├── service/
│   │   │   │       │   ├── ProductService.java
│   │   │   │       │   ├── InventoryService.java
│   │   │   │       │   └── validation/
│   │   │   │       │       ├── ProductValidator.java
│   │   │   │       │       └── InventoryValidator.java
│   │   │   │       └── Report/
│   │   │   │           ├── InventoryReportService.java
│   │   │   │           └── EmailService.java
│   │   │   └── resources/
│   │   │       ├── application.properties
│   │   │       └── stockmanagement.sql
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
└── frontend/
    ├── public/
    │   └── index.html
    ├── src/
    │   ├── App.js
    │   ├── pages/
    │   │   ├── LoginPage.jsx
    │   │   ├── RegisterPage.jsx
    │   │   ├── DashboardPage.jsx
    │   │   ├── ProductsPage.jsx
    │   │   ├── AddProductPage.jsx
    │   │   ├── ReportsPage.jsx
    │   │   └── AddUserPage.jsx
    │   ├── components/
    │   │   ├── Navbar.jsx
    │   │   ├── Sidebar.jsx
    │   │   ├── ProductForm.jsx
    │   │   ├── ProductTable.jsx
    │   │   ├── ChartComponent.jsx
    │   │   ├── SummaryCard.jsx
    │   │   └── LowStockBadge.jsx
    │   ├── context/
    │   │   ├── AuthContext.js
    │   │   ├── InventoryContext.jsx
    │   │   ├── ThemeContext.js
    │   │   └── ToastContext.jsx
    │   ├── hooks/
    │   │   ├── useAuth.js
    │   │   ├── useProducts.js
    │   │   └── useReports.js
    │   ├── services/
    │   │   ├── AuthService.js
    │   │   ├── ProductService.js
    │   │   └── UserService.js
    │   ├── utilities/
    │   │   ├── ApiUtils.js
    │   │   ├── Constants.js
    │   │   ├── StorageUtils.js
    │   │   ├── ValidationUtils.js
    │   │   └── FormatUtils.js
    │   └── styles/
    │       ├── global.css
    │       ├── dashboard.css
    │       ├── inventory.css
    │       └── login.css
    └── package.json
```

---

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
1. User logs in and receives a token
2. Token is stored on the client
3. Token is sent with each request
4. Backend validates and authorizes the request

### Role Capabilities

| Action | CUSTOMER | SUPPLIER | ADMIN |
|---|---|---|---|
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

### Database Setup

```sql
CREATE DATABASE stockmanagement;
```

### Email Configuration
- Enable Gmail 2FA
- Generate an App Password
- Insert credentials into `EmailService`

### Backend Setup

```bash
cd backend
./mvnw spring-boot:run
```

Runs on: `http://localhost:8080`

### Frontend Setup

```bash
cd frontend
npm install
npm start
```

Runs on: `http://localhost:3000`

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