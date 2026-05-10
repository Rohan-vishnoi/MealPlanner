# 🍽️ Scalable Meal Planner Web App (AI-Powered)

An enterprise-grade, highly scalable web application for planning meals. Built with a modern Angular frontend and a robust Spring Boot Java backend. It features an integrated AI Assistant powered by the Gemini LLM to review meal plans, estimate caloric intake, and provide healthy suggestions.

## 🚀 Tech Stack
- **Frontend:** Angular (TypeScript)
- **Backend:** Spring Boot (Java)
- **Primary Database:** PostgreSQL (Relational SQL)
- **Caching Layer:** Redis (In-memory data store for high concurrency)
- **Remote API / AI:** Google AI Studio (Gemini REST API)
- **Architecture:** Client-Server, RESTful API, Stateless Microservices

## 🏗️ Architecture (High-Level Design for High Concurrency)
To support massive scale (up to millions of users), the application utilizes a stateless backend and heavy caching to reduce database load.

**Data Flow:**
`Client (Angular) ↔ Load Balancer ↔ Spring Boot API (Stateless) ↔ [Redis Cache & PostgreSQL & Gemini API]`

1. **Angular Frontend (Client):** A Single Page Application (SPA) served via a CDN. It manages local state, UI routing, and makes asynchronous HTTP requests to the backend.
2. **Load Balancer:** Distributes incoming traffic across multiple Spring Boot instances to prevent any single server from being overwhelmed.
3. **Spring Boot Backend (API):** - Completely stateless (session data is not stored on the server).
    - Exposes RESTful endpoints (`/api/meals`, `/api/ai/review`).
    - Handles business logic, authentication, and security.
4. **Data Layer:**
    - **PostgreSQL:** The persistent, single source of truth for all user and meal data. Uses Connection Pooling (HikariCP) to manage database connections efficiently.
    - **Redis:** Caches frequently requested data (like default meal types or recent user meal plans) so the database isn't hit for every single request.
    - **Gemini API:** Remote LLM for nutritional analysis.

## 🤖 AI Assistant Integration Workflow
1. The user inputs their daily meals via the Angular UI.
2. The user clicks "Review Plan with AI".
3. Angular sends a POST request to the Spring Boot endpoint (`/api/ai/review`).
4. Spring Boot intercepts the request, verifies the user, and constructs a precise text prompt containing the meal data.
5. Spring Boot makes a synchronous, secure server-to-server HTTP call to the Gemini API.
6. Spring Boot parses Gemini's response, formats it into a structured JSON DTO, and returns it to Angular.
7. Angular renders the feedback in the UI.

## 🗄️ Database Schema (Low-Level Design)

### PostgreSQL Tables
*(Note: In a web app, we must track users, unlike a single-device mobile app).*

**`users`**
- `id`: UUID (Primary Key)
- `email`: VARCHAR (Unique)
- `password_hash`: VARCHAR

**`meal_types`**
- `id`: INT (Primary Key)
- `name`: VARCHAR (e.g., "Breakfast", "Lunch")

**`planned_meals`**
- `id`: UUID (Primary Key)
- `user_id`: UUID (Foreign Key -> users.id)
- `date`: DATE
- `meal_type_id`: INT (Foreign Key -> meal_types.id)
- `food_description`: TEXT

### Redis Cache Keys
- `user_meals:{user_id}:{date}` -> JSON string of the day's meals (Expires in 24 hours).

## 📁 Project Structure (Monorepo)
```text
meal-planner-web/
│
├── frontend/                     # Angular Application
│   ├── src/
│   │   ├── app/
│   │   │   ├── components/       # MealCard, AiReviewCard
│   │   │   ├── services/         # MealService, AiService (HTTP calls)
│   │   │   └── models/           # TypeScript Interfaces
│   │   └── environments/         # API URLs
│
└── backend/                      # Spring Boot Application
    ├── src/main/java/com/mealplanner/
    │   ├── controllers/          # REST Endpoints (MealController, AiController)
    │   ├── services/             # Business Logic & Gemini API integration
    │   ├── repositories/         # Spring Data JPA Interfaces
    │   ├── models/               # JPA Entities (User, PlannedMeal)
    │   └── config/               # Redis, Security, and CORS configurations
    └── src/main/resources/
        └── application.yml       # DB connection, API keys, Server settings
```

## 🚀 Scaling Strategies Implemented
- **Statelessness:** By using JWTs (JSON Web Tokens) for authentication, Spring Boot servers don't remember user sessions. We can spin up 100 servers, and any server can handle any request.
- **Connection Pooling:** Prevents the database from crashing under the weight of thousands of simultaneous connections.
- **Caching:** Redis intercepts requests for data that hasn't changed, delivering it in milliseconds and protecting PostgreSQL.
