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