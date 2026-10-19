# Airbnb Clone

A full-stack vacation-rental platform inspired by Airbnb. Guests can search and book stays, and hosts can publish and manage their properties. It's built with **Angular 17 + PrimeNG** on the frontend and **Spring Boot 3 + PostgreSQL** on the backend, and uses **Auth0** for authentication and role management.

![Angular](https://img.shields.io/badge/Angular-17-DD0031?logo=angular&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.2-6DB33F?logo=springboot&logoColor=white)
![Java](https://img.shields.io/badge/Java-21-ED8B00?logo=openjdk&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)
![Auth0](https://img.shields.io/badge/Auth0-OAuth2-EB5424?logo=auth0&logoColor=white)

## Features

**For guests (tenants)**
- Browse listings by category
- Search by location, dates, number of guests, beds, and bathrooms
- Check availability and book a stay
- View and cancel upcoming bookings

**For hosts (landlords)**
- Create listings through a multi-step wizard with photo upload and a map-based location picker
- Manage and delete properties
- See and cancel reservations made on their properties

**Platform**
- OAuth2 login with Auth0, including role-based access (tenant / landlord)
- Domain-driven backend structure (`user`, `listing`, `booking`, `sharedkernel`)
- Database schema versioned with Liquibase

## Tech stack

| Layer | Technologies |
|---|---|
| Frontend | Angular 17 (standalone components, signals), PrimeNG, PrimeFlex, Leaflet, Font Awesome, Day.js |
| Backend | Spring Boot 3.2, Spring Security + OAuth2 Client (Okta/Auth0 starter), Spring Data JPA, MapStruct, Liquibase |
| Database | PostgreSQL (run through Docker Compose in dev) |
| Auth | Auth0 |

## Repository structure

```
.
├── frontend/   # Angular 17 single-page app
└── backend/    # Spring Boot 3 REST API
```

## Getting started

### Prerequisites
- Node.js 20+ and Angular CLI 17 (`npm i -g @angular/cli@17`)
- JDK 21
- Docker (for the local PostgreSQL instance)
- An [Auth0](https://auth0.com/) tenant with a regular web application and a `ROLE_LANDLORD` role

### 1. Configure Auth0
In `backend/src/main/resources/application.yml`, point `okta.oauth2.issuer` at your Auth0 domain and set `application.auth0.role-landlord-id` to your landlord role ID. Add `http://localhost:4200/login/oauth2/code/okta` as an allowed callback URL in Auth0.

### 2. Run the backend
```bash
cd backend
./mvnw spring-boot:run -Dspring-boot.run.arguments="--AUTH0_CLIENT_ID=<client-id> --AUTH0_CLIENT_SECRET=<client-secret>"
```
Spring Boot's Docker Compose support starts PostgreSQL from `compose.yaml` automatically. Liquibase then creates the schema. The API runs on `http://localhost:8080`.

### 3. Run the frontend
```bash
cd frontend
npm install
npm start
```
Open `http://localhost:4200`. The dev server proxies `/api`, `/oauth2` and `/login` to the backend, and reloads automatically when you change a source file.

> **Using IntelliJ?** Instead of passing the arguments on the command line, add `AUTH0_CLIENT_ID` and `AUTH0_CLIENT_SECRET` as environment variables in the backend's run configuration.

## Building for production

```bash
# Frontend: output goes to frontend/dist/
cd frontend && npm run build

# Backend: runnable jar goes to backend/target/
cd backend && ./mvnw clean package
```

The `prod` Spring profile reads its database connection from the `POSTGRES_URL`, `POSTGRES_DB`, `POSTGRES_USER` and `POSTGRES_PASSWORD` environment variables.

## API overview

| Base path | Purpose |
|---|---|
| `/api/auth` | Current user, logout |
| `/api/tenant-listing` | Browse, view and search listings |
| `/api/landlord-listing` | Create, list and delete a host's listings |
| `/api/booking` | Create, list, cancel bookings; availability checks |
