#  👨🏻‍💻 HackerRank Challenge - Backend Developer AI
# 📦 Product Detail API - Mercado Libre Challenge

This repository contains a RESTful Backend API designed to support a high-traffic **Product Detail Page (PDP)**, inspired by Mercado Libre's architecture. It focuses on scalability, clean code, and robust error handling.

---

## 🛠️ Tech Stack

* **Language:** Java 21
* **Framework:** Spring Boot 3.3.5
* **Database:** H2 (In-Memory for portability)
* **Persistence:** Spring Data JPA
* **Documentation:** SpringDoc OpenAPI (Swagger UI)
* **Testing:** JUnit 5, Mockito, Spring Boot Test
* **Build Tool:** Maven

---

## 🏗️ Architecture & Design

The project follows a **Layered Architecture** to ensure separation of concerns and maintainability.

```mermaid
classDiagram
    ProductController ..> ProductService
    ProductService ..> ProductRepository
    ProductService ..> SellerRepository
    ProductService ..> CategoryRepository
    ProductService ..> ProductMapper
    
    class ProductController {
        +getProduct(id)
        +createProduct(dto)
    }
    class ProductService {
        +findProductById(id)
        +createProduct(dto)
    }
    class ProductMapper {
        +mapToDTO(entity)
        +mapToEntity(dto)
    }
  ```
Key Architectural Decisions
DTO Pattern & Data Protection:

Instead of creating separate classes for Input/Output (e.g., ProductRequest, ProductResponse), I utilized Jackson's @JsonProperty(access = READ_ONLY) on fields like id and createdAt. This keeps the codebase concise without compromising security (users cannot inject IDs or timestamps).

Smart Seeding (Lazy Creation):

Categories: Strict validation. Products must belong to an existing category (Master Data) to preserve the catalog structure.

Sellers: Lazy creation. If a seller nickname doesn't exist during product creation, the system creates it on-the-fly. This improves the Developer Experience (DX) when seeding data for testing.

Value Objects (DDD):

Price is modeled as an @Embeddable Value Object, encapsulating business logic (validation, formatting) and ensuring Amount and Currency always go together.

Error Handling:

Implemented a global ControllerAdvice to transform exceptions (ResourceNotFoundException, MethodArgumentNotValidException) into clean, standardized JSON responses (HTTP 404, 400).

🗄️ Data Model (ER Diagram)
Fragmento de código

erDiagram
    PRODUCT ||--|| PRICE : embeds
    PRODUCT }|--|| CATEGORY : belongs_to
    PRODUCT }|--|| SELLER : sold_by
    PRODUCT ||--|{ PRODUCT_ATTRIBUTE : has
    PRODUCT ||--|{ IMAGES : contains
    
    PRODUCT {
        long id
        string title
        int stock
        enum condition
    }
    PRICE {
        decimal amount
        string currency
    }
    CATEGORY {
        long id
        string name
    }
    SELLER {
        long id
        string nickname
    }
🚀 How to Run
Option 1: Using Maven (Recommended)
Make sure you have Java 21 installed.

Bash

./mvnw clean spring-boot:run
Option 2: Using Docker
(If you have Docker installed)

Bash

# 1. Build the image
docker build -t ml-challenge .

# 2. Run container
docker run -p 8080:8080 ml-challenge
The application will start at http://localhost:8080.

📚 API Documentation
This project includes Swagger UI for interactive testing.

👉 Access Swagger UI: http://localhost:8080/swagger-ui/index.html

Quick Test (CURL)
1. Create a Product (POST) Copy and paste this into your terminal to seed a product:

Bash

curl -X POST http://localhost:8080/products/create \
-H "Content-Type: application/json" \
-d '{
  "title": "Samsung Galaxy S23 Ultra",
  "description": "El smartphone más potente de Samsung con cámara de 200MP.",
  "price": {
    "amount": 1200.00,
    "currency": "USD"
  },
  "stockQuantity": 50,
  "soldQuantity": 0,
  "condition": { "value": "NEW" },
  "category": { "id": 1 },
  "seller": { "nickname": "OfficialSamsungStore", "page": "[http://samsung.com](http://samsung.com)" },
  "attributes": [
    { "name": "Marca", "value": "Samsung" },
    { "name": "Color", "value": "Phantom Black" },
    { "name": "Memoria", "value": "256GB" }
  ],
  "images": ["[http://http2.mlstatic.com/D_NQ_NP_s23.jpg](http://http2.mlstatic.com/D_NQ_NP_s23.jpg)"]
}'
2. Get Product Detail (GET) Use the ID returned from the previous step (usually 1):

Bash

curl -X GET http://localhost:8080/products/1
✅ Testing
The project includes Unit Tests (Service Layer) and Integration Tests (Controller Layer).

To run all tests:

Bash

./mvnw test
Author: Gabriel Franco Emiliano Acevedo De la TORRE
