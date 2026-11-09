# Workbase Database Design

## 1. Overview

Workbase uses MongoDB as its database and Mongoose as its Object Data Modeling (ODM) library.

The database is divided into collections. Each collection stores a specific type of application data.

## 2. Database Collections

### 2.1 Users Collection

Stores global user account information.

Fields:

- `name`: User's full name.
- `email`: User's email address.
- `passwordHash`: Securely hashed password.
- `avatar`: User profile image.
- `isActive`: Account status.
- `createdAt`: Account creation date.
- `updatedAt`: Last update date.

A user account can belong to multiple organizations.

### 2.2 Organizations Collection

Stores organization information.

Fields:

- `name`: Organization name.
- `slug`: Unique organization identifier.
- `ownerId`: Reference to the owner in the Users collection.
- `plan`: Organization subscription plan.
- `settings`: Organization-specific settings.
- `createdAt`: Creation date.
- `updatedAt`: Last update date.

### 2.3 Memberships Collection

Connects users with organizations.

Fields:

- `organizationId`: Reference to an organization.
- `userId`: Reference to a user.
- `roleIds`: Array of role references.
- `status`: Membership status.
- `joinedAt`: Membership creation date.

A user can belong to multiple organizations.

A user can have different roles in different organizations.

A unique compound index will be created on `organizationId` and `userId` to prevent duplicate memberships.

### 2.4 Roles Collection

Stores organization-specific roles and permissions.

Fields:

- `organizationId`: Reference to the organization.
- `name`: Role name.
- `permissions`: Array of permission identifiers.
- `description`: Role description.
- `createdAt`: Creation date.
- `updatedAt`: Last update date.

Examples of permissions:

- `projects:read`
- `projects:create`
- `projects:update`
- `projects:delete`
- `tasks:read`
- `tasks:create`
- `tasks:update`
- `tasks:delete`

### 2.5 Invitations Collection

Stores invitations sent to potential organization members.

Fields:

- `organizationId`: Reference to an organization.
- `email`: Invited user's email.
- `tokenHash`: Hashed invitation token.
- `roleIds`: Roles assigned through the invitation.
- `status`: Invitation status.
- `expiresAt`: Invitation expiration date.
- `createdAt`: Invitation creation date.

### 2.6 Projects Collection

Stores organization projects.

Fields:

- `organizationId`: Reference to the organization.
- `name`: Project name.
- `description`: Project description.
- `status`: Project status.
- `createdBy`: Reference to the user who created the project.
- `createdAt`: Creation date.
- `updatedAt`: Last update date.

Every project must belong to an organization.

### 2.7 Tasks Collection

Stores tasks associated with projects.

Fields:

- `organizationId`: Reference to the organization.
- `projectId`: Reference to the project.
- `title`: Task title.
- `description`: Task description.
- `assigneeId`: Reference to the assigned user.
- `status`: Task status.
- `priority`: Task priority.
- `dueDate`: Task deadline.
- `createdAt`: Creation date.
- `updatedAt`: Last update date.

Every task must belong to an organization and a project.

### 2.8 Audit Logs Collection

Stores important activities performed by users.

Fields:

- `organizationId`: Reference to the organization.
- `actorId`: User who performed the action.
- `action`: Action performed.
- `resourceType`: Type of resource affected.
- `resourceId`: ID of the affected resource.
- `metadata`: Additional activity information.
- `createdAt`: Activity timestamp.

## 3. Database Relationships

- One user can belong to multiple organizations.
- One organization can have multiple members.
- One organization can have multiple roles.
- One membership can have multiple roles.
- One organization can have multiple projects.
- One project can have multiple tasks.
- One organization can have multiple invitations.
- One organization can have multiple audit log entries.

## 4. Multi-Tenant Data Isolation

Workbase follows a multi-tenant database architecture.

Every organization-owned document must contain an `organizationId`.

All organization-scoped database queries must filter using the authenticated user's authorized organization.

The backend must verify membership before allowing access to organization resources.

Users must never be able to access another organization's data by changing an organization ID in an API request.

## 5. Database Indexing

The following indexes will be considered:

- Unique index on `users.email`.
- Unique index on `organizations.slug`.
- Compound unique index on `memberships.organizationId` and `memberships.userId`.
- Index on `projects.organizationId`.
- Compound index on `tasks.organizationId` and `tasks.projectId`.
- Index on `auditLogs.organizationId` and `auditLogs.createdAt`.

Indexes will be added based on query patterns and performance requirements.

## 6. Data Integrity

- References must point to valid documents.
- A task must belong to the same organization as its project.
- A membership must reference an existing user and organization.
- Roles must belong to the organization in which they are assigned.
- Organization-scoped operations must verify tenant ownership.