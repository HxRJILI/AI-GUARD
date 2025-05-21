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
            "capture_image_path": "/api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f/image",
            "device_id": "device-123",
            "notes": ""
          },
          // More log entries...
        ],
        "total": 235,
        "page": 1,
        "limit": 20,
        "pages": 12
      }

   :reqheader Authorization: JWT token
   :query page: Page number (default: 1)
   :query limit: Items per page (default: 20, max: 100)
   :query start_date: Filter by start date (YYYY-MM-DD)
   :query end_date: Filter by end date (YYYY-MM-DD)
   :query user_id: Filter by user ID
   :query location_id: Filter by location ID
   :query access_granted: Filter by access result (true/false)
   :query min_confidence: Filter by minimum confidence score
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Get Access Log Detail
-------------------

.. http:get:: /api/logs/(id)

   Retrieves details of a specific access log entry.

   **Example request**:

   .. sourcecode:: http

      GET /api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f",
        "user_id": "7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d",
        "user": {
          "first_name": "John",
          "last_name": "Smith",
          "email": "john.smith@example.com",
          "role": "SECURITY_OFFICER",
          "department": "Security"
        },
        "location_id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
        "location": {
          "name": "Main Entrance",
          "description": "Front entrance to Administration building",
          "building": "Administration",
          "floor": "1"
        },
        "timestamp": "2025-05-21T14:32:45Z",
        "access_granted": true,
        "confidence_score": 0.92,
        "capture_image_path": "/api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f/image",
        "device_id": "device-123",
        "device": {
          "name": "Entry Camera 1",
          "type": "Camera"
        },
        "notes": "",
        "previous_access": "2025-05-21T08:45:33Z",
        "related_alerts": []
      }

   :reqheader Authorization: JWT token
   :param id: Log entry ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: log entry not found

Get Access Log Image
------------------

.. http:get:: /api/logs/(id)/image

   Retrieves the captured image for a specific log entry.

   **Example request**:

   .. sourcecode:: http

      GET /api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f/image HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: image/jpeg

      (binary image data)

   :reqheader Authorization: JWT token
   :param id: Log entry ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: log entry or image not found

Update Access Log Notes
---------------------

.. http:patch:: /api/logs/(id)

   Updates notes for a specific log entry.

   **Example request**:

   .. sourcecode:: http

      PATCH /api/logs/f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "notes": "User verified by security officer on duty."
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f",
        "notes": "User verified by security officer on duty.",
        "updated_at": "2025-05-21T15:10:22Z",
        "updated_by": "admin-user-id"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param id: Log entry ID
   :statuscode 200: notes updated successfully
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: log entry not found

Export Access Logs
----------------

.. http:get:: /api/logs/export

   Exports access logs to CSV or Excel format.

   **Example request**:

   .. sourcecode:: http

      GET /api/logs/export?format=csv&start_date=2025-05-01&end_date=2025-05-21&location_id=a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: text/csv
      Content-Disposition: attachment; filename="access_logs_2025-05-01_2025-05-21.csv"

      id,user_id,user_name,location_name,timestamp,access_granted,confidence_score,device_id,notes
      f1e2d3c4-b5a6-7f8e-9d0c-1b2a3c4d5e6f,"7a1b2c3d-4e5f-6a7b-8c9d-0e1f2a3b4c5d","John Smith","Main Entrance","2025-05-21T14:32:45Z",true,0.92,"device-123",""
      // More log entries...

   :reqheader Authorization: JWT token
   :query format: Export format (csv or excel, default: csv)
   :query start_date: Filter by start date (YYYY-MM-DD, required)
   :query end_date: Filter by end date (YYYY-MM-DD, required)
   :query user_id: Filter by user ID (optional)
   :query location_id: Filter by location ID (optional)
   :query access_granted: Filter by access result (true/false, optional)
   :statuscode 200: successful operation
   :statuscode 400: invalid parameters
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Alerts
=====

Get Alerts
---------

.. http:get:: /api/alerts

   Retrieves alerts with pagination and filtering.

   **Example request**:

   .. sourcecode:: http

      GET /api/alerts?page=1&limit=20&severity=HIGH&status=OPEN HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "items": [
          {
            "id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
            "type": "unauthorized_access",
            "severity": "HIGH",
            "location_id": "location-id-1",
            "location": {
              "name": "Server Room",
              "building": "IT Center"
            },
            "related_log_id": "log-id-1",
            "timestamp": "2025-05-21T13:45:22Z",
            "message": "Unauthorized access attempt at Server Room",
            "status": "OPEN",
            "acknowledged_by": null,
            "acknowledged_at": null,
            "resolved_by": null,
            "resolved_at": null,
            "notes": ""
          },
          // More alerts...
        ],
        "total": 42,
        "page": 1,
        "limit": 20,
        "pages": 3
      }

   :reqheader Authorization: JWT token
   :query page: Page number (default: 1)
   :query limit: Items per page (default: 20, max: 100)
   :query type: Filter by alert type
   :query severity: Filter by severity (LOW, MEDIUM, HIGH, CRITICAL)
   :query status: Filter by status (OPEN, ACKNOWLEDGED, RESOLVED)
   :query start_date: Filter by start date (YYYY-MM-DD)
   :query end_date: Filter by end date (YYYY-MM-DD)
   :query location_id: Filter by location ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Get Alert Detail
--------------

.. http:get:: /api/alerts/(id)

   Retrieves details of a specific alert.

   **Example request**:

   .. sourcecode:: http

      GET /api/alerts/a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
        "type": "unauthorized_access",
        "severity": "HIGH",
        "location_id": "location-id-1",
        "location": {
          "name": "Server Room",
          "description": "Main server room with restricted access",
          "building": "IT Center",
          "floor": "2"
        },
        "related_log_id": "log-id-1",
        "related_log": {
          "id": "log-id-1",
          "timestamp": "2025-05-21T13:45:22Z",
          "confidence_score": 0.45,
          "access_granted": false,
          "capture_image_path": "/api/logs/log-id-1/image"
        },
        "timestamp": "2025-05-21T13:45:22Z",
        "message": "Unauthorized access attempt at Server Room",
        "details": {
          "attempt_count": 1,
          "potential_match": {
            "user_id": "user-id-1",
            "name": "John Smith",
            "confidence": 0.45
          },
          "device_id": "device-id-1",
          "device_name": "Server Room Camera"
        },
        "status": "OPEN",
        "acknowledged_by": null,
        "acknowledged_at": null,
        "resolved_by": null,
        "resolved_at": null,
        "notes": ""
      }

   :reqheader Authorization: JWT token
   :param id: Alert ID
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: alert not found

Acknowledge Alert
---------------

.. http:post:: /api/alerts/(id)/acknowledge

   Acknowledges an alert.

   **Example request**:

   .. sourcecode:: http

      POST /api/alerts/a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d/acknowledge HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "notes": "Looking into this alert."
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
        "status": "ACKNOWLEDGED",
        "acknowledged_by": "current-user-id",
        "acknowledged_at": "2025-05-21T14:15:30Z",
        "notes": "Looking into this alert."
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param id: Alert ID
   :statuscode 200: alert acknowledged successfully
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: alert not found
   :statuscode 409: alert already acknowledged

Resolve Alert
-----------

.. http:post:: /api/alerts/(id)/resolve

   Resolves an alert.

   **Example request**:

   .. sourcecode:: http

      POST /api/alerts/a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d/resolve HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "resolution": "False alarm. Authorized maintenance personnel."
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "id": "a1b2c3d4-e5f6-7a8b-9c0d-1e2f3a4b5c6d",
        "status": "RESOLVED",
        "resolved_by": "current-user-id",
        "resolved_at": "2025-05-21T14:30:45Z",
        "notes": "False alarm. Authorized maintenance personnel."
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param id: Alert ID
   :statuscode 200: alert resolved successfully
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: alert not found
   :statuscode 409: alert already resolved

Create Alert
----------

.. http:post:: /api/alerts

   Creates a new manual alert.

   **Example request**:

   .. sourcecode:: http

      POST /api/alerts HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "type": "security_concern",
        "severity": "MEDIUM",
        "location_id": "location-id-2",
        "message": "Suspicious individual in parking area",
        "details": {
          "description": "Person in dark clothing loitering near vehicles",
          "reported_by": "Security Guard"
        }
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 201 Created
      Content-Type: application/json

      {
        "id": "b2c3d4e5-f6a7-8b9c-0d1e-2f3a4b5c6d7e",
        "type": "security_concern",
        "severity": "MEDIUM",
        "location_id": "location-id-2",
        "timestamp": "2025-05-21T14:45:30Z",
        "message": "Suspicious individual in parking area",
        "details": {
          "description": "Person in dark clothing loitering near vehicles",
          "reported_by": "Security Guard"
        },
        "status": "OPEN",
        "created_by": "current-user-id"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :statuscode 201: alert created successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions