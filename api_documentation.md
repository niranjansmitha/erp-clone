# PeopleOne Backend API Documentation

This document provides a comprehensive overview of all API endpoints exposed via the **API Gateway** (`http://localhost:8080`). All services are stateless and rely on JWT tokens for authentication.

## Global Requirements
- **Base URL:** `http://localhost:8080/api/v1`
- **Authentication:** All endpoints (except `/auth/**` and `/polls/**` GET requests) require a JWT token passed in the `Authorization` header as a Bearer token:
  `Authorization: Bearer <your_jwt_token>`

---

## 1. Authentication Service (`/auth`)

Handles user registration and login. No authentication required.

| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/auth/register` | Register a new user | Public |
| `POST` | `/auth/login` | Login and receive a JWT token | Public |

---

## 2. User Service (`/users`)

Manages employee profiles and hierarchy.

| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `GET` | `/users` | Get a list of all employees | `USER`, `ADMIN` |
| `GET` | `/users/{id}` | Get an employee's details by ID | `USER`, `ADMIN` |
| `PUT` | `/users/{id}` | Update employee profile (First/Last name) | `USER`, `ADMIN` |
| `DELETE` | `/users/{id}` | Delete an employee | `ADMIN` |
| `GET` | `/users/{id}/manager-id` | Retrieve the ID of the employee's manager | `USER`, `ADMIN` |

---

## 3. Attendance Service (`/leaves`)

Manages employee leave requests and balances.

| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/leaves` | Apply for a leave (requires `employeeId`, `managerId`, dates, reason) | `USER`, `ADMIN` |
| `GET` | `/leaves/employee/{employeeId}` | Get all leave requests for a specific employee | `USER`, `ADMIN` |
| `PUT` | `/leaves/{id}/status?status={status}` | Approve or reject a leave request | `ADMIN` |

---

## 4. Communication Service (`/announcements`)

Manages corporate announcements and personalized feeds.

| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/announcements` | Create a new announcement | `ADMIN` |
| `GET` | `/announcements/{id}` | Get a specific announcement | `USER`, `ADMIN` |
| `GET` | `/announcements/feed` | Get personalized feed (Params: `employeeId`, `departmentId`, `page`, `size`) | `USER`, `ADMIN` |
| `GET` | `/announcements/category/{cat}` | Get announcements by category (Params: `page`, `size`) | `USER`, `ADMIN` |
| `PUT` | `/announcements/{id}` | Update an announcement | `ADMIN` |
| `DELETE` | `/announcements/{id}` | Delete an announcement | `ADMIN` |

---

## 5. Knowledge Service (`/policies`)

Manages company policy documents.

| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `GET` | `/policies` | Get all policy documents | `USER`, `ADMIN` |
| `GET` | `/policies/{id}` | Get a specific policy document | `USER`, `ADMIN` |
| `POST` | `/policies` | Upload/Create a new policy document | `ADMIN` |
| `PUT` | `/policies/{id}` | Update a policy document | `ADMIN` |
| `DELETE` | `/policies/{id}` | Delete a policy document | `ADMIN` |
| `GET` | `/policies/category/{category}`| Get policies by category | `USER`, `ADMIN` |
| `GET` | `/policies/search?title={title}`| Search policies by title | `USER`, `ADMIN` |

---

## 6. Engagement Service (`/kudos`, `/badges`, `/polls`)

Manages employee engagement features like peer recognition, awards, and polling.

### Kudos
| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/kudos` | Send a kudos to another employee | `USER`, `ADMIN` |
| `GET` | `/kudos` | View all kudos in the company | `USER`, `ADMIN` |
| `GET` | `/kudos/employee/{id}` | View all kudos received by an employee | `USER`, `ADMIN` |
| `PUT` | `/kudos/{id}` | Update a kudos message | `USER`, `ADMIN` |
| `DELETE` | `/kudos/{id}` | Delete a kudos | `ADMIN` |

### Badges
| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/badges` | Award a badge to an employee | `ADMIN` |
| `GET` | `/badges` | Get all badges awarded | `USER`, `ADMIN` |
| `GET` | `/badges/employee/{id}` | Get all badges belonging to an employee | `USER`, `ADMIN` |
| `PUT` | `/badges/{id}` | Update a badge | `ADMIN` |
| `DELETE` | `/badges/{id}` | Delete a badge | `ADMIN` |

### Polls
| Method | Endpoint | Description | Roles |
| :--- | :--- | :--- | :--- |
| `POST` | `/polls` | Create a new poll | `ADMIN` |
| `GET` | `/polls` | Get all polls (publicly accessible) | `Public` |
| `GET` | `/polls/{id}` | Get a specific poll | `Public` |
| `PUT` | `/polls/{id}` | Update an existing poll | `ADMIN` |
| `DELETE` | `/polls/{id}` | Delete a poll | `ADMIN` |
| `POST` | `/polls/{id}/vote` | Submit a vote for a poll | `USER`, `ADMIN` |
| `GET` | `/polls/{id}/results` | View real-time results for a poll | `USER`, `ADMIN` |

---

## Information Flow (Microservices Architecture)
1. **Frontend to API Gateway:** The frontend makes a request to `localhost:8080/api/v1/...` including the JWT token.
2. **API Gateway Auth Filter:** The Gateway intercepts the request, validates the JWT signature, extracts the user's ID and Roles, and injects them as HTTP Headers (`X-User-Id`, `X-User-Roles`).
3. **Gateway Routing:** The Gateway forwards the request to the target microservice based on the URL path.
4. **Service Header Filter:** The target microservice (e.g. `attendance-service`) intercepts the request, reads the injected headers, and populates the Spring `SecurityContext`.
5. **Controller Layer:** The request reaches the Controller, where `@PreAuthorize` annotations validate if the user has the required roles (`USER` or `ADMIN`) before processing the logic.
