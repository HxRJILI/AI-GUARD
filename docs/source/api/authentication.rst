==============
Authentication
==============

The API uses JWT (JSON Web Tokens) for authentication.

Login Endpoint
=============

.. http:post:: /api/auth/login

   Authenticates a user and returns access and refresh tokens.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/login HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "email": "admin@example.com",
        "password": "secure_password"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "user": {
          "id": "5f8d0d55-e9a2-4d4a-9e5b-8d1f1c3d5e7f",
          "name": "Admin User",
          "email": "admin@example.com",
          "role": "ADMIN"
        }
      }

   :reqheader Content-Type: application/json
   :statuscode 200: successful authentication
   :statuscode 401: invalid credentials
   :statuscode 429: too many failed attempts

Token Refresh
============

.. http:post:: /api/auth/refresh

   Refreshes an expired access token using a valid refresh token.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/refresh HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
      }

   :reqheader Content-Type: application/json
   :statuscode 200: successful token refresh
   :statuscode 401: invalid refresh token

Authentication Middleware
=======================

All protected endpoints require the JWT token in the Authorization header:

.. sourcecode:: http

   GET /api/users HTTP/1.1
   Host: api.aiguard.example.com
   Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...

Token Payload Structure
=====================

.. code-block:: json

   {
     "sub": "5f8d0d55-e9a2-4d4a-9e5b-8d1f1c3d5e7f",
     "name": "Admin User",
     "email": "admin@example.com",
     "role": "ADMIN",
     "permissions": ["system.configure", "users.manage", "enrollments.create", "logs.view"],
     "iat": 1621506000,
     "exp": 1621509600
   }

Two-Factor Authentication
=======================

.. http:post:: /api/auth/2fa/generate

   Generates a TOTP secret for a user.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/2fa/generate HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {}

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "secret": "JBSWY3DPEHPK3PXP",
        "qr_code_url": "data:image/png;base64,iVBORw0KGgoA..."
      }

.. http:post:: /api/auth/2fa/verify

   Verifies a TOTP code and enables 2FA for a user.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/2fa/verify HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {
        "code": "123456"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Two-factor authentication enabled"
      }

.. http:post:: /api/auth/2fa/login

   Completes the login process with a TOTP code when 2FA is enabled.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/2fa/login HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "email": "admin@example.com",
        "password": "secure_password",
        "code": "123456"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
        "user": {
          "id": "5f8d0d55-e9a2-4d4a-9e5b-8d1f1c3d5e7f",
          "name": "Admin User",
          "email": "admin@example.com",
          "role": "ADMIN"
        }
      }

Logout
=====

.. http:post:: /api/auth/logout

   Invalidates the current refresh token.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/logout HTTP/1.1
      Host: api.aiguard.example.com
      Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
      Content-Type: application/json

      {}

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Successfully logged out"
      }

Password Reset
============

.. http:post:: /api/auth/password/forgot

   Initiates the password reset process.

   **Example request**:

   .. sourcecode:: http

      POST /api/auth/password/forgot HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "email": "admin@example.com"
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "success": true,
        "message": "Password reset link sent to email"
      }

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