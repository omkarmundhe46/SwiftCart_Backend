# SwiftCart Backend

A RESTful e-commerce backend built with **Spring Boot 3.5.3**, **Java 21**, **Spring Data JPA**, and **MySQL**. The application provides CRUD APIs for products, brands, main categories, subcategories, users, testimonials, contact-us submissions, and newsletter subscriptions, with support for multipart image uploads and a local upload directory.

> This README is based on the current implementation in the repository. It documents the APIs and architecture that are actually present rather than assuming features that are not implemented.

## Overview

SwiftCart Backend follows a layered Spring Boot architecture:

```text
Client / Frontend
       |
       v
REST Controllers
       |
       v
Service Layer
       |
       v
Repository Layer
       |
       v
MySQL Database

Multipart Images
       |
       v
Local ./uploads/ directory
```

The repository contains a Maven-based Spring Boot application and a SQL seed script (`e-com.sql`) with sample brands, categories, products, users, testimonials, contact submissions, and newsletter records.

## Features

- Product CRUD operations
- Product image upload using multipart requests
- Brand CRUD with image upload
- Main-category CRUD with image upload
- Sub-category CRUD with image upload
- User signup, retrieval, profile update, and deletion
- Contact-us submission and retrieval
- Newsletter subscription and management
- Testimonial CRUD with image upload
- DTO-based request and response models
- ModelMapper dependency for DTO/entity mapping
- Bean Validation dependency for request validation
- Global exception-handling infrastructure
- Spring Boot Actuator dependency for application monitoring
- CORS configuration for a local frontend at `http://localhost:3000`
- Static serving of uploaded files through `/uploads/**`

## Technology Stack

| Technology | Purpose |
|---|---|
| Java 21 | Application language |
| Spring Boot 3.5.3 | Backend framework |
| Spring Web | REST APIs and HTTP handling |
| Spring Data JPA | Persistence and ORM |
| Hibernate | JPA implementation through Spring Boot |
| MySQL | Relational database |
| Maven | Build and dependency management |
| Lombok | Boilerplate reduction |
| ModelMapper 3.1.0 | DTO/entity mapping |
| Bean Validation | Request validation |
| Spring Boot Actuator | Application monitoring endpoints |
| MultipartFile | Image/file uploads |

## Project Structure

```text
SwiftCart_Backend/
├── .mvn/
├── mvnw
├── mvnw.cmd
├── pom.xml
├── e-com.sql
└── src/
    └── main/
        ├── java/com/ecom/
        │   ├── EComBackendApplication.java
        │   ├── controller/
        │   │   ├── BrandController.java
        │   │   ├── ContactusController.java
        │   │   ├── MaincategoryController.java
        │   │   ├── Newsletter.java
        │   │   ├── ProductController.java
        │   │   ├── SignupController.java
        │   │   ├── SubcategoryController.java
        │   │   └── TestimonialController.java
        │   ├── entities/
        │   ├── exception/
        │   ├── global_config/
        │   ├── modal/
        │   ├── repository/
        │   └── services/
        └── resources/
            └── application.properties
```

### Main layers

**Controllers** expose REST endpoints and translate HTTP requests into service calls.

**Services** contain the business logic and repository interaction.

**Repositories** handle persistence through Spring Data JPA.

**Entities** model database data such as `Product`, `Brand`, `Maincategory`, `Subcategory`, `User`, `Testimonial`, `Contactus`, and `Newsletter`.

**DTOs / Response DTOs** separate API payloads from persistence entities.

**Exception package** contains custom exceptions, upload helpers, and global exception-handling infrastructure.

**Global configuration** contains CORS and static upload-resource configuration.

## API Endpoints

### Products — `/product`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/product` | Create a product; accepts multipart data and optional images |
| GET | `/product` | Get all products |
| GET | `/product/{id}` | Get a product by ID |
| PUT | `/product/{id}` | Update a product with multipart data and optional images |
| DELETE | `/product/{id}` | Delete a product |

Product create/update requests use `multipart/form-data` with:

- `data` — JSON payload for the product DTO
- `pic` — optional array of image files

A product response contains fields such as name, categories, brand, pricing, stock, description, stock quantity, image paths, and active status.

### Brands — `/brand`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/brand` | Create a brand with an image |
| GET | `/brand` | Get all brands |
| PUT | `/brand/{id}` | Update a brand with an image |
| DELETE | `/brand/{id}` | Delete a brand |

### Main Categories — `/maincategory`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/maincategory` | Create a main category with an image |
| GET | `/maincategory` | Get all main categories |
| PUT | `/maincategory/{id}` | Update a main category with an image |
| DELETE | `/maincategory/{id}` | Delete a main category |

### Subcategories — `/subcategory`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/subcategory` | Create a subcategory with an image |
| GET | `/subcategory` | Get all subcategories |
| PUT | `/subcategory/{id}` | Update a subcategory with an image |
| DELETE | `/subcategory/{id}` | Delete a subcategory |

### Users — `/user`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/user` | Register a user |
| GET | `/user` | Get all users |
| GET | `/user/{userid}` | Get a user by ID |
| PUT | `/user/{userid}` | Update a user profile with optional image |
| DELETE | `/user/{userid}` | Delete a user |

The profile update endpoint uses multipart data with a `data` JSON part and an optional `pic` file.

### Contact Us — `/contactus`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/contactus` | Save contact-us details |
| GET | `/contactus` | Get all contact-us records |

### Newsletter — `/newsletter`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/newsletter` | Subscribe/register an email |
| GET | `/newsletter` | Get all newsletter records |
| GET | `/newsletter/{id}` | Get a newsletter record by ID |
| DELETE | `/newsletter/{id}` | Delete a newsletter record |

### Testimonials — `/testimonial`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/testimonial` | Create a testimonial with an image |
| GET | `/testimonial` | Get all testimonials |
| PUT | `/testimonial/{id}` | Update a testimonial with an image |
| DELETE | `/testimonial/{id}` | Delete a testimonial |

## Database

The application is configured for MySQL with the current local settings in `application.properties`:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/e-com_db
spring.datasource.username=root
spring.datasource.password=pass
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

### Database setup

Create the database before starting the application:

```sql
CREATE DATABASE e-com_db;
```

The repository also includes `e-com.sql` containing sample data for the application's core tables.

> The SQL file currently starts with `USE ducart_db1;`, while the application properties point to `e-com_db`. Update the SQL database name to match your local configuration before importing it, or change the application configuration accordingly.

## Image Uploads

Images are stored under the application's working directory:

```text
./uploads/
```

The upload helper creates folders dynamically, and Spring MVC exposes the directory through:

```text
/uploads/**
```

For example, an uploaded brand image may be available at a path such as:

```text
/uploads/brands/nike.png
```

This design is suitable for local development. For production, object storage such as AWS S3 or another managed file-storage service is a stronger option.

## CORS Configuration

The current `WebConfig` allows requests from:

```text
http://localhost:3000
```

and permits all HTTP methods and headers with credentials enabled.

For production, replace the local origin with the actual deployed frontend origin and avoid broad `*` method/header policies unless they are explicitly required.

## Getting Started

### Prerequisites

Install:

- Java 21
- MySQL 8.x or a compatible MySQL server
- Git

Verify the Java installation:

```bash
java -version
```

Verify Maven through the included Maven Wrapper without requiring a global Maven installation.

### 1. Clone the repository

```bash
git clone https://github.com/omkarmundhe46/SwiftCart_Backend.git
cd SwiftCart_Backend
```

### 2. Create the database

```sql
CREATE DATABASE `e-com_db`;
```

### 3. Configure credentials

Edit:

```text
src/main/resources/application.properties
```

Set the database URL, username, and password for your local MySQL instance.

For safer development, move credentials to environment variables or an untracked profile-specific configuration file.

### 4. Build the project

Linux/macOS:

```bash
./mvnw clean package
```

Windows:

```bat
mvnw.cmd clean package
```

### 5. Run the application

Linux/macOS:

```bash
./mvnw spring-boot:run
```

Windows:

```bat
mvnw.cmd spring-boot:run
```

Or run the generated JAR:

```bash
java -jar target/e-com-backend-0.0.1-SNAPSHOT.jar
```

## Example Requests

### Create a user

```http
POST /user
Content-Type: application/json
```

Example body:

```json
{
  "name": "John Doe",
  "username": "john",
  "email": "john@example.com",
  "phone": "9999999999"
}
```

The exact required fields depend on the current `SignupDTO` validation rules.

### Create a product with images

```http
POST /product
Content-Type: multipart/form-data
```

Multipart parts:

```text
data = { ...product JSON... }
pic  = product-image-1.jpg
pic  = product-image-2.jpg
```

### Get all products

```http
GET /product
```

## Error Handling

The project contains a global exception handler under `com.ecom.exception`, along with:

- `ResourceNotFoundException`
- `ErrorDetails`
- `FileUploadUtil`
- `GlobelExceptionHandler`

These classes provide the foundation for centralized error responses and file-upload support.

The current exception handler should be reviewed before production use; standard HTTP status handling and clearer error payloads would make the API more reliable for consumers.

## Validation

The project includes `spring-boot-starter-validation`, and user signup currently uses `@Valid` on the request DTO. Validation coverage can be expanded consistently across product, category, brand, testimonial, and contact/newsletter payloads.

## Monitoring

Spring Boot Actuator is included as a dependency. Additional Actuator endpoint configuration can be applied depending on the deployment and monitoring requirements.

## Current Configuration and Production Notes

The repository is configured primarily for local development. Before production deployment, review the following:

- Move MySQL credentials out of `application.properties`.
- Do not commit real passwords or other secrets.
- Replace local CORS configuration with the deployed frontend origin.
- Move image storage from local disk to object storage such as S3.
- Add authentication and authorization around administrative CRUD endpoints if they are intended to be protected.
- Add pagination/filtering/search for product and category APIs as the catalog grows.
- Add service/repository/controller tests and integration tests.
- Improve exception responses and use correct HTTP status codes consistently.
- Add API documentation with OpenAPI/Swagger.
- Add database migrations with Flyway or Liquibase rather than relying only on `ddl-auto=update`.
- Reduce DEBUG logging in production.
- Add structured logging and observability.
- Add Docker support and CI/CD for repeatable deployment.

## Important Repository Observations

### 1. Database-name mismatch in seed SQL

The application points to `e-com_db`, while `e-com.sql` currently uses `ducart_db1`. These should be aligned before importing the sample data.

### 2. Local filesystem uploads

Images are persisted below the current working directory. This can cause problems with ephemeral deployment environments or multiple application instances.

### 3. No authentication/security layer is visible in the current dependency set

The current Maven configuration includes Spring Web, JPA, validation, Actuator, MySQL, Lombok, and ModelMapper, but does not include Spring Security or a JWT library. Administrative endpoints should therefore be reviewed carefully before exposing the application publicly.

### 4. Newsletter controller wiring should be checked

`Newsletter.java` declares a `NewsletterService` field without constructor or field injection in the current source. This should be corrected before relying on those endpoints in a deployed environment.

## Suggested Architecture Improvements

A production-oriented version could evolve toward:

```text
Frontend
   |
   v
API / Authentication Layer
   |
   v
Controllers
   |
   v
Services
   |
   +------> Validation / Mapping
   |
   v
Repositories
   |
   v
MySQL

          +------------------+
          | Object Storage   |
          | S3 / Cloudinary  |
          +------------------+
```

Recommended next steps include authentication/authorization, cleaner DTO validation, centralized exception responses, pagination, search/filter APIs, managed image storage, database migrations, OpenAPI documentation, automated tests, and containerized deployment.

## Development Workflow

Typical development cycle:

```text
Update entity/DTO
      |
      v
Update repository if needed
      |
      v
Implement service logic
      |
      v
Expose/adjust controller endpoint
      |
      v
Run application
      |
      v
Test with Postman / frontend
      |
      v
Verify MySQL data and uploaded files
```

## Author

**Omkar Ramesh Mundhe**

GitHub: [@omkarmundhe46](https://github.com/omkarmundhe46)

## License

No explicit open-source license is currently defined in this repository. Add a `LICENSE` file if you want to distribute the project under a specific license.
