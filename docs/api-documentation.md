# Workbase API Documentation

## 1. Overview

Workbase provides a REST API for authentication, organization management, membership management, role management, projects, tasks, and activity tracking.

### Base URL

Development:

`http://localhost:3000/api/v1`

All API endpoints return JSON responses.

## 2. Authentication

Protected endpoints require an authenticated user.

Authentication will use JWT-based authentication with HTTP-only cookies.

### POST /auth/register

Registers a new user.

**Authentication:** Not required.

**Request Body:**

```json
{
  "name": "Aqua",
  "email": "aqua@example.com",
  "password": "StrongPassword123!"
}
```

**Success Response:** `201 Created`

```json
{
  "message": "Registration successful",
  "user": {
    "id": "user_id",
    "name": "Aqua",
    "email": "aqua@example.com"
  }
}
```

### POST /auth/login

Authenticates an existing user.

**Authentication:** Not required.

**Request Body:**

```json
{
  "email": "aqua@example.com",
  "password": "StrongPassword123!"
}
```

**Success Response:** `200 OK`

Returns the authenticated user's information and sets the authentication cookie.

### POST /auth/logout

Logs out the authenticated user.

**Authentication:** Required.

**Success Response:** `200 OK`

```json
{
  "message": "Logged out successfully"
}
```

### GET /auth/me

Returns the currently authenticated user's profile.

**Authentication:** Required.

**Success Response:** `200 OK`

## 3. Organization Management

### POST /organizations

Creates a new organization.

**Authentication:** Required.

**Request Body:**

```json
{
  "name": "Example Company"
}
```

**Success Response:** `201 Created`

Returns the created organization.

### GET /organizations

Returns organizations the authenticated user belongs to.

**Authentication:** Required.

**Success Response:** `200 OK`

Returns an array of organizations.

## 4. Membership Management

### POST /organizations/:organizationId/invitations

Invites a user to an organization.

**Authentication:** Required.

**Permission:** `members:invite`

**Request Body:**

```json
{
  "email": "member@example.com",
  "roleIds": ["role_id"]
}
```

**Success Response:** `201 Created`

Returns the created invitation.

### GET /organizations/:organizationId/members

Returns members of an organization.

**Authentication:** Required.

**Permission:** `members:read`

**Success Response:** `200 OK`

Returns an array of organization memberships.

## 5. Role Management

### GET /organizations/:organizationId/roles

Returns roles belonging to an organization.

**Authentication:** Required.

**Permission:** `roles:read`

**Success Response:** `200 OK`

Returns an array of roles.

### POST /organizations/:organizationId/roles

Creates a custom role.

**Authentication:** Required.

**Permission:** `roles:create`

**Request Body:**

```json
{
  "name": "Project Manager",
  "permissions": [
    "projects:read",
    "projects:create",
    "projects:update",
    "tasks:read",
    "tasks:create"
  ]
}
```

**Success Response:** `201 Created`

Returns the created role.

## 6. Project Management

### POST /organizations/:organizationId/projects

Creates a project.

**Authentication:** Required.

**Permission:** `projects:create`

**Request Body:**

```json
{
  "name": "Website Redesign",
  "description": "Redesign the company website",
  "status": "active"
}
```

**Success Response:** `201 Created`

Returns the created project.

### GET /organizations/:organizationId/projects

Returns projects belonging to an organization.

**Authentication:** Required.

**Permission:** `projects:read`

**Success Response:** `200 OK`

Returns an array of projects.

## 7. Task Management

### POST /organizations/:organizationId/projects/:projectId/tasks

Creates a task inside a project.

**Authentication:** Required.

**Permission:** `tasks:create`

**Request Body:**

```json
{
  "title": "Design homepage",
  "description": "Create the homepage design",
  "priority": "high",
  "status": "todo",
  "assigneeId": "user_id",
  "dueDate": "2026-12-01T00:00:00.000Z"
}
```

**Success Response:** `201 Created`

Returns the created task.

### GET /organizations/:organizationId/projects/:projectId/tasks

Returns tasks belonging to a project.

**Authentication:** Required.

**Permission:** `tasks:read`

**Success Response:** `200 OK`

Returns an array of tasks.

## 8. Audit Logs

### GET /organizations/:organizationId/audit-logs

Returns organization activity logs.

**Authentication:** Required.

**Permission:** `auditLogs:read`

**Success Response:** `200 OK`

Returns an array of audit log entries.

## 9. Common HTTP Status Codes

| Status | Meaning |
|---|---|
| 200 | Request successful |
| 201 | Resource created |
| 400 | Invalid request data |
| 401 | Authentication required |
| 403 | Permission denied |
| 404 | Resource not found |
| 409 | Resource conflict |
| 500 | Internal server error |

## 10. Security Rules

- Protected endpoints require authentication.
- Organization membership must be verified.
- Required permissions must be checked on the backend.
- Organization IDs supplied by clients must never be trusted without verification.
- Resources must belong to the requested organization.
- Request data must be validated before processing.
- Sensitive information must not be returned in API responses.

**Note:** This document describes the planned API contract. Actual implementation may introduce changes as development progresses.