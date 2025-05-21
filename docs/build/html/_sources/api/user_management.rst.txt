===============
User Management
===============

API endpoints for managing users in the AI-Guard system.

Create User
==========

.. http:post:: /api/users

   Creates a new user.

   **Example request**:

   .. sourcecode:: http

      POST /api/users HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com",
        "role": "SECURITY_OFFICER",
        "department": "Security",
        "active": true
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 201 Created
      Content-Type: application/json

      {
        "id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com",
        "role": "SECURITY_OFFICER",
        "department": "Security",
        "active": true,
        "created_at": "2025-05-21T10:30:00Z",
        "updated_at": "2025-05-21T10:30:00Z"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :statuscode 201: user created successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 409: email already exists

Get Users
========

.. http:get:: /api/users

   Retrieves a list of users with pagination.

   **Example request**:

   .. sourcecode:: http

      GET /api/users?page=1&limit=20&department=Security HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "items": [
          {
            "id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
            "first_name": "John",
            "last_name": "Smith",
            "email": "john.smith@example.com",
            "role": "SECURITY_OFFICER",
            "department": "Security",
            "active": true,
            "created_at": "2025-05-21T10:30:00Z",
            "updated_at": "2025-05-21T10:30:00Z"
          },
          {
            "id": "8b2c3d4e-5f6a-7b8c-9d0e-1f2a3b4c5d6e",
            "first_name": "Jane",
            "last_name": "Doe",
            "email": "jane.doe@example.com",
            "role": "SECURITY_OFFICER",
            "department": "Security",
            "active": true,
            "created_at": "2025-05-20T15:45:00Z",
            "updated_at": "2025-05-20T15:45:00Z"
          }
          // More users...
        ],
        "total": 45,
        "page": 1,
        "limit": 20,
        "pages": 3
      }

   :reqheader Authorization: JWT token
   :query page: Page number (default: 1)
   :query limit: Items per page (default: 20, max: 100)
   :query name: Filter by name (optional)
   :query email: Filter by email (optional)
   :query role: Filter by role (optional)
   :query department: Filter by department (optional)
   :query active: Filter by active status (optional)
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Get User
=======

.. http:get:: /api/users/(id)

   Retrieves a specific user by ID.

   **Example request**:

   .. sourcecode:: http

      GET /api/users/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com",
        "role": "SECURITY_OFFICER",
        "department": "Security",
        "active": true,
        "created_at": "2025-05-21T10:30:00Z",
        "updated_at": "2025-05-21T10:30:00Z",
        "enrollment_status": {
          "enrolled": true,
          "quality_score": 0.92,
          "last_updated": "2025-05-21T10:35:22Z"
        },
        "access_summary": {
          "total_accesses": 17,
          "successful_accesses": 15,
          "denied_accesses": 2,
          "last_access": "2025-05-21T08:45:33Z"
        }
      }

   :reqheader Authorization: JWT token
   :param id: User ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found

Update User
==========

.. http:patch:: /api/users/(id)

   Updates a user.

   **Example request**:

   .. sourcecode:: http

      PATCH /api/users/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "department": "IT Security",
        "active": false
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "first_name": "John",
        "last_name": "Smith",
        "email": "john.smith@example.com",
        "role": "SECURITY_OFFICER",
        "department": "IT Security",
        "active": false,
        "created_at": "2025-05-21T10:30:00Z",
        "updated_at": "2025-05-21T14:45:12Z"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param id: User ID
   :statuscode 200: user updated successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found

Delete User
==========

.. http:delete:: /api/users/(id)

   Deletes a user.

   **Example request**:

   .. sourcecode:: http

      DELETE /api/users/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "User deleted successfully"
      }

   :reqheader Authorization: JWT token
   :param id: User ID
   :statuscode 200: user deleted successfully
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found

Bulk User Import
==============

.. http:post:: /api/users/import

   Imports multiple users from a CSV file.

   **Example request**:

   .. sourcecode:: http

      POST /api/users/import HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="file"; filename="users.csv"
      Content-Type: text/csv

      first_name,last_name,email,role,department,active
      John,Smith,john.smith@example.com,SECURITY_OFFICER,Security,true
      Jane,Doe,jane.doe@example.com,SECURITY_OFFICER,Security,true
      ------WebKitFormBoundary7MA4YWxkTrZu0gW--

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "imported": 2,
        "failed": 0,
        "errors": []
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: multipart/form-data
   :statuscode 200: import completed
   :statuscode 400: invalid file format
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions