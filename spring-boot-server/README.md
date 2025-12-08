# Project Documentation: Angular 17 + Spring Boot + MySQL CRUD Application

## 📋 About the App

This is a **full-stack Tutorial Management CRUD Application** that demonstrates a modern, production-ready architecture combining:
- **Frontend**: Angular 17 with Bootstrap
- **Backend**: Spring Boot 3.1.5 with Spring Data JPA
- **Database**: MySQL 8.0
- **Containerization**: Docker & Docker Compose

The application allows users to create, read, update, and delete (CRUD) tutorial records stored in a MySQL database, with a responsive Angular web interface communicating with a RESTful Spring Boot API.

---

## What It Does

The application provides a complete tutorial management system with the following capabilities:

### Core Features
- **Create Tutorials**: Add new tutorials with title, description, and publication status
- **Read Tutorials**: Browse all tutorials or search by title
- **Update Tutorials**: Edit tutorial details and toggle publication status
- **Delete Tutorials**: Remove individual tutorials or clear all records
- **Filter by Status**: View only published or unpublished tutorials
- **Search Functionality**: Find tutorials by partial title matching

### Key Attributes
Each tutorial contains:
- **ID**: Auto-generated unique identifier
- **Title**: Tutorial name
- **Description**: Tutorial content/summary
- **Published**: Boolean status indicating publication state

---

## 🏗️ Architecture & Components

### Overall Architecture

```
┌─────────────────┐
│   Web Browser   │
└────────┬────────┘
         │ HTTP/HTTPS
         ▼
┌─────────────────────────────────────┐
│      Angular 17 Frontend            │
│  ├─ UI Components                   │
│  ├─ Routing                         │
│  └─ HTTP Client Service             │
└────────┬────────────────────────────┘
         │ REST API (Port 8081)
         ▼
┌─────────────────────────────────────┐
│    Spring Boot Backend (Port 8080)  │
│  ├─ Controller (REST Endpoints)     │
│  ├─ Service/Repository Layer        │
│  └─ Data Access Objects             │
└────────┬────────────────────────────┘
         │ JDBC/JPA
         ▼
┌─────────────────────────────────────┐
│       MySQL Database (Port 3306)    │
│  ├─ tutorials table                 │
│  └─ Connection pool                 │
└─────────────────────────────────────┘
```

### Frontend Components (Angular)

#### **1. App Module** (`app.module.ts`)
Root module that bootstraps the entire application and declares all components.

#### **2. App Routing** (`app-routing.module.ts`)
Defines navigation routes between components:
- `/` → Tutorials list view
- `/add` → Add new tutorial form
- `/tutorials/:id` → Tutorial detail view with edit capability

#### **3. Core Components**

| Component | Purpose | Responsibilities |
|-----------|---------|------------------|
| **TutorialsList** | Main list view | Display all tutorials, handle filtering, manage list actions |
| **Add-Tutorial** | Creation form | Collect new tutorial data, submit to backend |
| **Tutorial-Details** | Detail/edit view | Show single tutorial, edit fields, toggle status, delete |


---

### Backend Components (Spring Boot)

#### **1. Controller** (`TutorialController.java`)
REST controller handling all HTTP requests with CORS enabled.

**Base Path**: `/api`

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/tutorials` | Get all tutorials (supports `?title=` search param) |
| `GET` | `/tutorials/{id}` | Get specific tutorial by ID |
| `GET` | `/tutorials/published` | Get all published tutorials |
| `POST` | `/tutorials` | Create new tutorial |
| `PUT` | `/tutorials/{id}` | Update existing tutorial |
| `DELETE` | `/tutorials/{id}` | Delete specific tutorial |
| `DELETE` | `/tutorials` | Delete all tutorials |

#### **2. Model** (`Tutorial.java`)
JPA Entity representing the tutorials table.

**Fields**:
- `id` - Long (Primary Key, Auto-generated)
- `title` - String
- `description` - String
- `published` - Boolean

#### **3. Repository** (`TutorialRepository.java`)
Spring Data JPA repository providing database access.

**Custom Query Methods**:
- `findByTitleContaining(String title)` - Search by title
- `findByPublished(boolean published)` - Filter by status

#### **4. Configuration**
Defined in `application.properties`:
- Database connection URL
- JPA/Hibernate settings
- DDL auto strategy (update)
- MySQL dialect

---

### Database Schema

#### **tutorials** Table
```sql
CREATE TABLE tutorials (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255),
  description VARCHAR(255),
  published BOOLEAN
);
```

**Indexes**:
- Primary Key on `id`
- Can add index on `published` for better filtering performance

---

## How to Run

### Prerequisites
- **Docker & Docker Compose** (recommended)
- OR
- Java 17+, Maven, Node.js 20+, Angular CLI 17, MySQL 8.0

### Option 1: Run with Docker Compose (Recommended)

#### Setup
1. Clone the repository:
```bash
git clone <repository-url>
cd app
```

2. Create `.env` file in the root directory:
```env
MYSQL_DATABASE=tutorial_db
MYSQL_PASSWORD=root123
SPRING_DB_USERNAME=root
SPRING_DB_PASSWORD=root123
```

3. Start all services:
```bash
docker-compose up -d
```

#### Access the Application
- **Frontend**: http://localhost:8080
- **Backend API**: http://localhost:8081/api
- **MySQL**: localhost:3306

#### Verify Services
```bash
docker-compose ps
docker-compose logs -f spring-boot  # View backend logs
docker-compose logs -f angular      # View frontend logs
```

#### Stop Services
```bash
docker-compose down
```

---

### Option 2: Run Locally (Development)

#### Backend Setup

1. Navigate to backend directory:
```bash
cd spring-boot-server
```

2. Set up MySQL:
```bash
# Ensure MySQL is running on localhost:3306
mysql -u root -p
CREATE DATABASE tutorial_db;
```

3. Update `application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/tutorial_db?useSSL=false&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=your_password
```

4. Run Maven:
```bash
mvn clean spring-boot:run
```

Backend runs on **http://localhost:8080**

#### Frontend Setup

1. Navigate to frontend directory:
```bash
cd angular-17-client
```

2. Install dependencies:
```bash
npm install
```

3. Start development server:
```bash
ng serve --port 4200
```

Frontend accessible at **http://localhost:4200**

---

## 📁 Project Structure

```
app/
├── .env                          # Environment variables
├── .env.example                  # Example environment file
├── docker-compose.yml            # Docker Compose configuration
├── README.md                     # Project README
├── DOCUMENTATION.md              # This file
│
├── .github/
│   └── workflows/
│       └── build-and-push.yml   # CI/CD pipeline
│
├── angular-17-client/            # Frontend Application
│   ├── package.json              # npm dependencies
│   ├── angular.json              # Angular CLI config
│   ├── tsconfig.json             # TypeScript config
│   ├── Dockerfile                # Frontend container
│   ├── nginx.conf                # Nginx server config
│   ├── .dockerignore
│   ├── .gitignore
│   │
│   └── src/
│       ├── index.html            # Main HTML
│       ├── main.ts               # Bootstrap file
│       ├── styles.css            # Global styles
│       │
│       └── app/
│           ├── app.module.ts                    # Root module
│           ├── app.component.ts                 # Root component
│           ├── app.component.html               # Root template
│           ├── app.component.css                # Root styles
│           ├── app-routing.module.ts            # Routing config
│           │
│           ├── components/                      # Feature components
│           │   ├── tutorials-list/
│           │   │   ├── tutorials-list.component.ts
│           │   │   ├── tutorials-list.component.html
│           │   │   ├── tutorials-list.component.css
│           │   │   └── tutorials-list.component.spec.ts
│           │   ├── add-tutorial/
│           │   │   ├── add-tutorial.component.ts
│           │   │   ├── add-tutorial.component.html
│           │   │   ├── add-tutorial.component.css
│           │   │   └── add-tutorial.component.spec.ts
│           │   └── tutorial-details/
│           │       ├── tutorial-details.component.ts
│           │       ├── tutorial-details.component.html
│           │       ├── tutorial-details.component.css
│           │       └── tutorial-details.component.spec.ts
│           │
│           ├── models/                         # Data models
│           │   ├── tutorial.model.ts
│           │   └── tutorial.model.spec.ts
│           │
│           └── services/                       # Business logic
│               ├── tutorial.service.ts
│               └── tutorial.service.spec.ts
│
└── spring-boot-server/           # Backend Application
    ├── pom.xml                   # Maven dependencies
    ├── Dockerfile                # Backend container
    ├── .dockerignore
    ├── .gitignore
    ├── mvnw                      # Maven wrapper
    ├── mvnw.cmd
    │
    └── src/
        ├── main/
        │   ├── java/
        │   │   └── com/bezkoder/spring/datajpa/
        │   │       ├── SpringBootDataJpaApplication.java  # Main class
        │   │       │
        │   │       ├── controller/
        │   │       │   └── TutorialController.java        # REST endpoints
        │   │       │
        │   │       ├── model/
        │   │       │   └── Tutorial.java                  # JPA entity
        │   │       │
        │   │       └── repository/
        │   │           └── TutorialRepository.java        # Data access
        │   │
        │   └── resources/
        │       └── application.properties                 # Spring config
        │
        └── test/
            └── java/
                └── com/bezkoder/spring/datajpa/
                    └── SpringBootDataJpaApplicationTests.java
```

---

## 🛠️ Technology Stack Summary

### Frontend Technologies
| Technology | Version | Purpose |
|-----------|---------|---------|
| **Angular** | 17.0.0 | Web framework |
| **TypeScript** | 5.2.2 | Language |
| **Bootstrap** | 4.6.2 | CSS framework |
| **RxJS** | 7.8.0 | Reactive programming |
| **Angular Router** | 17.0.0 | Routing |
| **Angular Forms** | 17.0.0 | Form handling |

**Build Tools**:
- Angular CLI 17.0.7
- Webpack (via Angular CLI)
- Karma (testing)
- Jasmine (unit testing)

**Development Environment**:
- Node.js 20+
- npm 9+

---

### Backend Technologies
| Technology | Version | Purpose |
|-----------|---------|---------|
| **Spring Boot** | 3.1.5 | Framework |
| **Spring Data JPA** | 3.1.5 | ORM/Data access |
| **Spring Web** | 3.1.5 | REST API |
| **MySQL Connector** | Latest | Database driver |
| **Java** | 17 | Language |

**Build Tool**:
- Maven 3.8+

**Testing**:
- JUnit 5
- Spring Boot Test

---

### Database
| Component | Version |
|-----------|---------|
| **MySQL** | 8.0 |
| **Connector** | mysql-connector-j (Latest) |

**Key Features**:
- InnoDB storage engine
- UTF-8 encoding
- Supports transactions

---

### DevOps & Containerization
| Technology | Purpose |
|-----------|---------|
| **Docker** | Container runtime |
| **Docker Compose** | Multi-container orchestration |
| **GitHub Actions** | CI/CD pipeline |

**Container Images**:
- `mysql:8.0` - Database container
- Custom Spring Boot image (built from Dockerfile)
- Custom Angular image (built with Nginx)

---

## 🔄 CI/CD Pipeline

**Workflow File**: `.github/workflows/build-and-push.yml`

### Pipeline Stages

1. **Checkout**: Clone repository code
2. **Setup Environments**: 
   - Set up JDK 17 for backend
   - Set up Node.js 20 for frontend
3. **Run Tests**:
   - Backend: `mvn test`
   - Frontend: `npm test -- --watch=false --browsers=ChromeHeadless`
4. **Build Docker Images**: Create images for both backend and frontend
5. **Push to GHCR**: Push images to GitHub Container Registry

### Triggered By
- Manual workflow dispatch (`workflow_dispatch`)

### Target Registry
- GitHub Container Registry (GHCR): `ghcr.io`

---