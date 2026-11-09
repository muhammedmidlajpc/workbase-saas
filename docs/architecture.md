# Workbase Architecture

## 1. Architecture Overview

Workbase follows a client-server architecture.

The application consists of three main layers:

1. Frontend
2. Backend
3. Database

Each layer has a specific responsibility.

## 2. Technology Stack

### Frontend
- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- TanStack Query

### Backend
- Node.js
- Fastify
- TypeScript
- Mongoose

### Database
- MongoDB

## 3. Frontend Architecture

The frontend is responsible for the user interface and user interactions.

Responsibilities:

- Display dashboards and application pages.
- Handle forms and user input.
- Send API requests to the backend.
- Display API responses.
- Manage loading and error states.
- Control UI elements based on user permissions.

The frontend must not be responsible for enforcing security permissions.

## 4. Backend Architecture

The backend handles application logic and security.

Responsibilities:

- Authenticate users.
- Verify organization membership.
- Check user permissions.
- Validate incoming requests.
- Process business logic.
- Communicate with the database.
- Return consistent API responses.
- Record important activities.

## 5. Database Architecture

MongoDB stores application data.

Mongoose is used to define schemas, models, validation, and database operations.

Main collections:

- Users
- Organizations
- Memberships
- Roles
- Invitations
- Projects
- Tasks
- Audit Logs

## 6. Request Flow

Example: Creating a project.

1. The user submits the project creation form.
2. The frontend sends an HTTP POST request.
3. The backend authenticates the user.
4. The backend verifies organization membership.
5. The backend checks the `projects:create` permission.
6. The backend validates the request data.
7. The backend creates the project in MongoDB.
8. The backend returns the created project.
9. The frontend updates the displayed project list.

## 7. Multi-Tenant Architecture

Workbase supports multiple organizations within one application.

Each organization has its own data.

Organization-owned documents must contain an `organizationId`.

Every tenant-scoped database query must enforce the correct organization boundary.

Users can belong to multiple organizations and have different roles in each organization.

## 8. Security Principles

- Passwords must be securely hashed.
- Authentication must be required for protected endpoints.
- Backend authorization must be enforced.
- Organization membership must be verified.
- Database queries must be scoped to the correct organization.
- Input validation must be performed on the backend.
- Sensitive configuration must be stored in environment variables.