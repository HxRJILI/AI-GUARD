# AI-GUARD RST File Examples (Part 4)

Continuing the API authentication password reset section and remaining RST files.

### api/authentication.rst (continued)
```rst
.. http:post:: /api/auth/password/reset

   Resets the password using a valid reset token.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/password/reset HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "password": "new_secure_password",
        "password_confirmation": "new_secure_password"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Password has been reset successfully"
      }

   :reqheader Content-Type: application/json
   :statuscode 200: password reset successful
   :statuscode 400: invalid token or password mismatch
   :statuscode 404: token not found

Security Considerations
=====================

- Access tokens expire after 1 hour
- Refresh tokens expire after 7 days
- Failed login attempts are rate-limited
- Token revocation is supported via the blacklist endpoint
- All authentication attempts are logged for security auditing

.. warning::
   Always transmit authentication credentials over HTTPS connections only.
```

### api/user_management.rst
```rst
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
```

### api/face_enrollment.rst
```rst
===============
Face Enrollment
===============

API endpoints for enrolling and managing face data.

Enroll Face
==========

.. http:post:: /api/enroll

   Enrolls a user's face into the system.

   **Example request**:

   .. sourcecode:: http

      POST /api/enroll HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="user_id"

      7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="images[]"; filename="front.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="images[]"; filename="left_45.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="images[]"; filename="right_45.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="angles[]"

      front
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="angles[]"

      left_45
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="angles[]"

      right_45
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="metadata"
      Content-Type: application/json

      {
        "glasses": true,
        "beard": false
      }
      ------WebKitFormBoundary7MA4YWxkTrZu0gW--

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 201 Created
      Content-Type: application/json

      {
        "success": true,
        "enrollment_id": "8e7d6c5b-4a3b-2c1d-0e9f-8e7d6c5b4a3b",
        "quality_scores": [0.92, 0.87, 0.89],
        "overall_quality": 0.89,
        "embedding_version": "facenet_v1.0"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: multipart/form-data
   :form user_id: ID of the user to enroll
   :form images[]: Face images to enroll (multiple files)
   :form angles[]: Angles corresponding to each image (e.g., front, left_45, right_45)
   :form metadata: Additional metadata as JSON (optional)
   :statuscode 201: enrollment successful
   :statuscode 400: invalid input data or poor quality images
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found

Get Enrollment Status
===================

.. http:get:: /api/enroll/status/(user_id)

   Retrieves the enrollment status for a user.

   **Example request**:

   .. sourcecode:: http

      GET /api/enroll/status/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "enrolled": true,
        "enrollment_date": "2025-05-21T10:35:22Z",
        "last_updated": "2025-05-21T10:35:22Z",
        "quality": 0.89,
        "angles_captured": ["front", "left_45", "right_45"],
        "embedding_version": "facenet_v1.0",
        "metadata": {
          "glasses": true,
          "beard": false
        }
      }

   :reqheader Authorization: JWT token
   :param user_id: User ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found or not enrolled

Delete Enrollment
===============

.. http:delete:: /api/enroll/(user_id)

   Deletes enrollment data for a user.

   **Example request**:

   .. sourcecode:: http

      DELETE /api/enroll/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Enrollment data deleted successfully"
      }

   :reqheader Authorization: JWT token
   :param user_id: User ID
   :statuscode 200: enrollment data deleted successfully
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found or not enrolled

Update Enrollment Metadata
=======================

.. http:patch:: /api/enroll/(user_id)/metadata

   Updates metadata associated with an enrollment.

   **Example request**:

   .. sourcecode:: http

      PATCH /api/enroll/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d/metadata HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "beard": true,
        "notes": "User now has a beard"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "metadata": {
          "glasses": true,
          "beard": true,
          "notes": "User now has a beard"
        }
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param user_id: User ID
   :statuscode 200: metadata updated successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found or not enrolled

Add Enrollment Image
==================

.. http:post:: /api/enroll/(user_id)/images

   Adds additional face images to an existing enrollment.

   **Example request**:

   .. sourcecode:: http

      POST /api/enroll/7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d/images HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="image"; filename="up_15.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="angle"

      up_15
      ------WebKitFormBoundary7MA4YWxkTrZu0gW--

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "quality_score": 0.91,
        "updated_overall_quality": 0.90
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: multipart/form-data
   :param user_id: User ID
   :form image: Face image to add
   :form angle: Angle of the captured image
   :statuscode 200: image added successfully
   :statuscode 400: invalid input data or poor quality image
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: user not found or not enrolled
```

### api/face_recognition.rst
```rst
=================
Face Recognition
=================

API endpoints for face recognition.

Recognize Face
============

.. http:post:: /api/recognize

   Recognizes a face in an image.

   **Example request**:

   .. sourcecode:: http

      POST /api/recognize HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="image"; filename="capture.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="location_id"

      a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="threshold"

      0.75
      ------WebKitFormBoundary7MA4YWxkTrZu0gW--

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "recognized": true,
        "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "confidence": 0.92,
        "user": {
          "first_name": "John",
          "last_name": "Smith",
          "role": "SECURITY_OFFICER",
          "department": "Security"
        },
        "access_granted": true,
        "log_id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: multipart/form-data
   :form image: Face image to recognize
   :form location_id: ID of the location where recognition is performed
   :form threshold: Recognition confidence threshold (optional, default: system setting)
   :statuscode 200: recognition processed
   :statuscode 400: invalid input data or no face detected
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Batch Recognition
===============

.. http:post:: /api/recognize/batch

   Recognizes multiple faces in multiple images.

   **Example request**:

   .. sourcecode:: http

      POST /api/recognize/batch HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW

      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="images[]"; filename="capture1.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="images[]"; filename="capture2.jpg"
      Content-Type: image/jpeg

      (binary image data)
      ------WebKitFormBoundary7MA4YWxkTrZu0gW
      Content-Disposition: form-data; name="location_id"

      a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d
      ------WebKitFormBoundary7MA4YWxkTrZu0gW--

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "results": [
          {
            "image_index": 0,
            "faces": [
              {
                "recognized": true,
                "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
                "confidence": 0.92,
                "user": {
                  "first_name": "John",
                  "last_name": "Smith"
                },
                "access_granted": true,
                "log_id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f"
              }
            ]
          },
          {
            "image_index": 1,
            "faces": [
              {
                "recognized": false,
                "confidence": 0.45,
                "access_granted": false,
                "log_id": "e2d3c4b5-a6f7-8e9d-0c1b-2a3c4d5e6f7"
              }
            ]
          }
        ]
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: multipart/form-data
   :form images[]: Face images to recognize (multiple files)
   :form location_id: ID of the location where recognition is performed
   :form threshold: Recognition confidence threshold (optional, default: system setting)
   :statuscode 200: recognition processed
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Recognition Stream
===============

.. http:post:: /api/recognize/stream

   Establishes a WebSocket connection for streaming recognition.

   WebSocket Protocol:
     ``wss://api.aiguard.example.com/api/recognize/stream?token=JWT_TOKEN&location_id=LOCATION_ID``

   **Client Message Format**:

   .. code-block:: json

      {
        "image": "base64_encoded_image_data",
        "timestamp": "2025-05-21T14:32:45.123Z"
      }

   **Server Response Format**:

   .. code-block:: json

      {
        "recognized": true,
        "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "confidence": 0.92,
        "user": {
          "first_name": "John",
          "last_name": "Smith",
          "role": "SECURITY_OFFICER"
        },
        "access_granted": true,
        "log_id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f",
        "timestamp": "2025-05-21T14:32:45.500Z"
      }

Recognition Thresholds
====================

.. http:get:: /api/recognize/thresholds

   Retrieves current recognition threshold settings.

   **Example request**:

   .. sourcecode:: http

      GET /api/recognize/thresholds HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "default": 0.75,
        "high_security": 0.85,
        "low_security": 0.65,
        "locations": {
          "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d": 0.80,
          "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e": 0.70
        }
      }

   :reqheader Authorization: JWT token
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

# AI-GUARD RST File Examples (Part 5)

Continuing from the Update Recognition Thresholds section in the API documentation.

### api/face_recognition.rst (continued)
```rst
Update Recognition Thresholds
===========================

.. http:put:: /api/recognize/thresholds

   Updates recognition threshold settings.

   **Example request**:

   .. sourcecode:: http

      PUT /api/recognize/thresholds HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "default": 0.78,
        "high_security": 0.88,
        "low_security": 0.68,
        "locations": {
          "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d": 0.82,
          "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e": 0.72
        }
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Threshold settings updated successfully",
        "settings": {
          "default": 0.78,
          "high_security": 0.88,
          "low_security": 0.68,
          "locations": {
            "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d": 0.82,
            "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e": 0.72
          }
        }
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :statuscode 200: settings updated successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Recognition Analytics
==================

.. http:get:: /api/recognize/analytics

   Retrieves recognition performance analytics.

   **Example request**:

   .. sourcecode:: http

      GET /api/recognize/analytics?start_date=2025-05-01&end_date=2025-05-21&location_id=a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "total_recognitions": 1235,
        "successful_recognitions": 1156,
        "failed_recognitions": 79,
        "average_confidence": 0.87,
        "confidence_distribution": {
          "0.5-0.6": 12,
          "0.6-0.7": 28,
          "0.7-0.8": 156,
          "0.8-0.9": 684,
          "0.9-1.0": 355
        },
        "by_hour": [
          {"hour": 0, "count": 15},
          {"hour": 1, "count": 8},
          // ... more hourly data
          {"hour": 23, "count": 22}
        ],
        "by_day": [
          {"date": "2025-05-01", "count": 58},
          {"date": "2025-05-02", "count": 62},
          // ... more daily data
          {"date": "2025-05-21", "count": 65}
        ]
      }

   :reqheader Authorization: JWT token
   :query start_date: Start date for analytics (YYYY-MM-DD, required)
   :query end_date: End date for analytics (YYYY-MM-DD, required)
   :query location_id: Filter by location ID (optional)
   :query user_id: Filter by user ID (optional)
   :statuscode 200: successful operation
   :statuscode 400: invalid parameters
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
```

### api/logs_alerts.rst
```rst
=============
Logs & Alerts
=============

API endpoints for managing access logs and security alerts.

Access Logs
==========

Get Access Logs
-------------

.. http:get:: /api/logs

   Retrieves access logs with pagination and filtering.

   **Example request**:

   .. sourcecode:: http

      GET /api/logs?page=1&limit=20&location_id=a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d&access_granted=true HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "items": [
          {
            "id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f",
            "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
            "user": {
              "first_name": "John",
              "last_name": "Smith",
              "role": "SECURITY_OFFICER"
            },
            "location_id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
            "location": {
              "name": "Main Entrance",
              "building": "Administration"
            },
            "timestamp": "2025-05-21T14:32:45Z",
            "access_granted": true,
            "confidence_score": 0.92,
            "capture_image_path": "/api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1