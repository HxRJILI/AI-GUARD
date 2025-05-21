================
System Settings
================

API endpoints for managing system configuration settings.

Get Settings
==========

.. http:get:: /api/settings

   Retrieves all system settings.

   **Example request**:

   .. sourcecode:: http

      GET /api/settings HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "recognition": {
          "default_threshold": 0.75,
          "high_security_threshold": 0.85,
          "low_security_threshold": 0.65,
          "min_face_size": 80,
          "detection_interval_ms": 200
        },
        "enrollment": {
          "min_quality_score": 0.7,
          "required_angles": ["front", "left_45", "right_45"],
          "re_enrollment_period_days": 365
        },
        "notifications": {
          "email_enabled": true,
          "sms_enabled": true,
          "push_enabled": true,
          "alert_recipients": ["security@example.com", "admin@example.com"]
        },
        "security": {
          "session_timeout_minutes": 30,
          "max_login_attempts": 5,
          "password_expiry_days": 90,
          "two_factor_required_roles": ["ADMIN", "SECURITY_OFFICER"]
        },
        "system": {
          "log_retention_days": 90,
          "backup_frequency": "daily",
          "maintenance_window": "01:00-03:00",
          "timezone": "UTC"
        }
      }

   :reqheader Authorization: JWT token
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Get Setting by Key
================

.. http:get:: /api/settings/(key)

   Retrieves a specific setting by key.

   **Example request**:

   .. sourcecode:: http

      GET /api/settings/recognition.default_threshold HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "key": "recognition.default_threshold",
        "value": 0.75,
        "description": "Default confidence threshold for face recognition",
        "updated_at": "2025-04-15T10:30:00Z",
        "updated_by": "admin-user-id"
      }

   :reqheader Authorization: JWT token
   :param key: Setting key
   :statuscode 200: successful operation
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: setting not found

Update Setting
============

.. http:put:: /api/settings/(key)

   Updates a specific setting.

   **Example request**:

   .. sourcecode:: http

      PUT /api/settings/recognition.default_threshold HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "value": 0.78
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "key": "recognition.default_threshold",
        "value": 0.78,
        "description": "Default confidence threshold for face recognition",
        "updated_at": "2025-05-21T15:30:00Z",
        "updated_by": "current-user-id"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :param key: Setting key
   :statuscode 200: setting updated successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions
   :statuscode 404: setting not found

Update Multiple Settings
=====================

.. http:put:: /api/settings

   Updates multiple settings at once.

   **Example request**:

   .. sourcecode:: http

      PUT /api/settings HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "recognition.default_threshold": 0.78,
        "recognition.high_security_threshold": 0.88,
        "security.session_timeout_minutes": 15
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "updated": [
          "recognition.default_threshold",
          "recognition.high_security_threshold",
          "security.session_timeout_minutes"
        ],
        "timestamp": "2025-05-21T15:35:22Z"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :statuscode 200: settings updated successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions

Reset Settings to Default
======================

.. http:post:: /api/settings/reset

   Resets settings to their default values.

   **Example request**:

   .. sourcecode:: http

      POST /api/settings/reset HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "keys": ["recognition.default_threshold", "recognition.high_security_threshold"]
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "reset": [
          {
            "key": "recognition.default_threshold",
            "value": 0.75
          },
          {
            "key": "recognition.high_security_threshold",
            "value": 0.85
          }
        ],
        "timestamp": "2025-05-21T15:40:15Z"
      }

   :reqheader Authorization: JWT token
   :reqheader Content-Type: application/json
   :statuscode 200: settings reset successfully
   :statuscode 400: invalid input data
   :statuscode 401: unauthorized
   :statuscode 403: insufficient permissions