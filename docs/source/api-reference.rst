API Reference
=============

AI Guard provides RESTful APIs for integration with external systems.

Authentication
--------------

All API endpoints require authentication using session-based authentication or API tokens.

**Session Authentication**

Login through the web interface to establish a session:

.. code-block:: http

   POST /api/auth/login
   Content-Type: application/json
   
   {
       "username": "admin",
       "password": "admin123"
   }

**API Token Authentication**

Include the API token in the Authorization header:

.. code-block:: http

   GET /api/surveillance/status
   Authorization: Bearer your-api-token-here

Surveillance API
----------------

Live Monitoring
~~~~~~~~~~~~~~~

**Get Surveillance Status**

.. code-block:: http

   GET /api/surveillance/status

Response:

.. code-block:: json

   {
       "status": "active",
       "camera_id": 0,
       "fps": 24.5,
       "faces_detected": 2,
       "last_frame_time": "2024-01-15T10:30:45Z",
       "uptime": "01:23:45"
   }

**Start Surveillance**

.. code-block:: http

   POST /api/surveillance/start
   Content-Type: application/json
   
   {
       "camera_id": 0,
       "recognition_threshold": 0.5,
       "detection_upsample": 1
   }

**Stop Surveillance**

.. code-block:: http

   POST /api/surveillance/stop

**Get Current Frame**

.. code-block:: http

   GET /api/surveillance/frame

Returns the current processed frame as JPEG image.

**Configure Parameters**

.. code-block:: http

   PUT /api/surveillance/config
   Content-Type: application/json
   
   {
       "recognition_threshold": 0.6,
       "detection_upsample": 2,
       "frame_skip": 3,
       "alert_cooldown": 30
   }

Alert Management
~~~~~~~~~~~~~~~~

**Get Active Alerts**

.. code-block:: http

   GET /api/alerts/active

Response:

.. code-block:: json

   {
       "alerts": [
           {
               "id": 123,
               "timestamp": "2024-01-15T10:30:45Z",
               "type": "unknown_person",
               "confidence": 0.85,
               "camera_id": 0,
               "snapshot_url": "/api/snapshots/123.jpg",
               "status": "active"
           }
       ],
       "count": 1
   }

**Get Alert History**

.. code-block:: http

   GET /api/alerts/history?start_date=2024-01-01&end_date=2024-01-15&limit=100

**Acknowledge Alert**

.. code-block:: http

   POST /api/alerts/123/acknowledge
   Content-Type: application/json
   
   {
       "handled_by": "guard1",
       "notes": "False alarm - maintenance worker"
   }

**Create Manual Alert**

.. code-block:: http

   POST /api/alerts/create
   Content-Type: application/json
   
   {
       "type": "manual",
       "description": "Suspicious activity reported",
       "camera_id": 0,
       "priority": "high"
   }

User Management API
-------------------

**Get User List** (Admin only)

.. code-block:: http

   GET /api/users

**Create User** (Admin only)

.. code-block:: http

   POST /api/users
   Content-Type: application/json
   
   {
       "username": "newuser",
       "password": "securepassword",
       "role": "surveillant",
       "full_name": "John Doe",
       "email": "john@example.com"
   }

**Update User** (Admin only)

.. code-block:: http

   PUT /api/users/123
   Content-Type: application/json
   
   {
       "role": "admin",
       "is_active": true
   }

**Delete User** (Admin only)

.. code-block:: http

   DELETE /api/users/123

Face Recognition API
--------------------

**Enroll New Face**

.. code-block:: http

   POST /api/faces/enroll
   Content-Type: multipart/form-data
   
   name: John Doe
   role: student
   student_id: 12345
   image: [image file]

**Get Enrolled Faces**

.. code-block:: http

   GET /api/faces/enrolled

**Update Face Information**

.. code-block:: http

   PUT /api/faces/123
   Content-Type: application/json
   
   {
       "name": "John Doe Jr.",
       "role": "staff",
       "is_active": true
   }

**Delete Face**

.. code-block:: http

   DELETE /api/faces/123

**Search Faces**

.. code-block:: http

   GET /api/faces/search?name=john&role=student

System API
-----------

**Get System Status**

.. code-block:: http

   GET /api/system/status

Response:

.. code-block:: json

   {
       "status": "healthy",
       "version": "1.0.0",
       "uptime": "2 days, 14:32:15",
       "cpu_usage": 45.2,
       "memory_usage": 62.8,
       "disk_usage": 34.1,
       "camera_status": "connected",
       "database_status": "healthy"
   }

**Get System Metrics**

.. code-block:: http

   GET /api/system/metrics

**Backup Database**

.. code-block:: http

   POST /api/system/backup

**Get Logs**

.. code-block:: http

   GET /api/system/logs?level=error&start_date=2024-01-01

WebSocket API
-------------

AI Guard uses WebSocket for real-time communication.

**Connection Endpoint**

.. code-block:: javascript

   const socket = io('http://localhost:5000');

**Events**

**Receive Real-time Alerts**

.. code-block:: javascript

   socket.on('new_alert', function(data) {
       console.log('Alert received:', data);
       // data contains: id, type, timestamp, confidence, etc.
   });

**Receive System Status Updates**

.. code-block:: javascript

   socket.on('system_status', function(data) {
       console.log('System status:', data);
       // data contains: fps, faces_detected, memory_usage, etc.
   });

**Send Configuration Updates**

.. code-block:: javascript

   socket.emit('update_config', {
       recognition_threshold: 0.6,
       detection_upsample: 1
   });

Error Handling
--------------

All API endpoints return appropriate HTTP status codes:

* **200 OK**: Request successful
* **201 Created**: Resource created successfully
* **400 Bad Request**: Invalid request parameters
* **401 Unauthorized**: Authentication required
* **403 Forbidden**: Insufficient permissions
* **404 Not Found**: Resource not found
* **500 Internal Server Error**: Server error

Error Response Format:

.. code-block:: json

   {
       "error": {
           "code": "INVALID_THRESHOLD",
           "message": "Recognition threshold must be between 0.3 and 0.7",
           "details": {
               "provided_value": 0.8,
               "valid_range": [0.3, 0.7]
           }
       }
   }

Rate Limiting
-------------

API endpoints are rate-limited to prevent abuse:

* **Authentication endpoints**: 5 requests per minute per IP
* **Surveillance endpoints**: 60 requests per minute per user
* **Alert endpoints**: 30 requests per minute per user
* **System endpoints**: 10 requests per minute per user

Rate limit headers are included in responses:

.. code-block:: http

   X-RateLimit-Limit: 60
   X-RateLimit-Remaining: 45
   X-RateLimit-Reset: 1642248000