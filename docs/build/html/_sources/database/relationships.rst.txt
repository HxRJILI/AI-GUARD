=============
Relationships
=============

The database implements several relationships between tables to maintain data integrity and support the system's functionality.

Entity Relationship Diagram (ERD)
===============================

.. figure:: /_static/database-erd.png
   :alt: Entity Relationship Diagram
   :align: center
   
   Entity Relationship Diagram for AI-Guard database

Key Relationships
===============

Users to Face Embeddings
----------------------

- One-to-many relationship
- A user can have multiple face embeddings (different angles, lighting conditions)
- All embeddings for a user are deleted when the user is deleted (CASCADE)

.. code-block:: sql
   :caption: User to face embeddings relationship

   -- In face_embeddings table
   CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE

Users to Access Logs
------------------

- One-to-many relationship
- A user can have multiple access log entries
- User reference in access logs is set to NULL if user is deleted (SET NULL)
- This preserves the historical record while removing personal identification

.. code-block:: sql
   :caption: User to access logs relationship

   -- In access_logs table
   CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL

Locations to Access Logs
----------------------

- One-to-many relationship
- A location can have multiple access log entries
- Required relationship (NOT NULL constraint on location_id)

.. code-block:: sql
   :caption: Location to access logs relationship

   -- In access_logs table
   CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id)

Locations to Devices
------------------

- One-to-many relationship
- A location can have multiple devices
- Optional relationship (location_id can be NULL)

.. code-block:: sql
   :caption: Location to devices relationship

   -- In devices table
   CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id)

Alerts to Various Entities
------------------------

Alerts have multiple relationships:

- **Alerts to Locations**: Alert may be associated with a specific location
- **Alerts to Access Logs**: Alert may reference a specific access log entry
- **Alerts to Users (acknowledged_by)**: References user who acknowledged the alert
- **Alerts to Users (resolved_by)**: References user who resolved the alert

.. code-block:: sql
   :caption: Alert relationships

   -- In alerts table
   CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id),
   CONSTRAINT fk_related_log FOREIGN KEY (related_log_id) REFERENCES access_logs(id),
   CONSTRAINT fk_acknowledged_by FOREIGN KEY (acknowledged_by) REFERENCES users(id),
   CONSTRAINT fk_resolved_by FOREIGN KEY (resolved_by) REFERENCES users(id)

Inheritance Relationships
======================

The system also implements table inheritance for specialized entity types:

Admin Users
---------

.. code-block:: sql
   :caption: Admin users inherit from users

   CREATE TABLE admin_users (
     id UUID PRIMARY KEY,
     super_admin BOOLEAN DEFAULT false,
     last_password_change TIMESTAMP,
     two_factor_enabled BOOLEAN DEFAULT true,
     CONSTRAINT fk_user FOREIGN KEY (id) REFERENCES users(id) ON DELETE CASCADE
   );

Security Officers
---------------

.. code-block:: sql
   :caption: Security officers inherit from users

   CREATE TABLE security_officers (
     id UUID PRIMARY KEY,
     badge_number VARCHAR(20) UNIQUE NOT NULL,
     clearance_level INTEGER DEFAULT 1,
     shift VARCHAR(20),
     CONSTRAINT fk_user FOREIGN KEY (id) REFERENCES users(id) ON DELETE CASCADE
   );

Polymorphic Relationships
======================

For flexible relationships, the system uses polymorphic associations:

Attachments
---------

.. code-block:: sql
   :caption: Polymorphic attachments table

   CREATE TABLE attachments (
     id UUID PRIMARY KEY,
     entity_type VARCHAR(50) NOT NULL,
     entity_id UUID NOT NULL,
     file_path VARCHAR(255) NOT NULL,
     file_type VARCHAR(50),
     file_size INTEGER,
     uploaded_by UUID REFERENCES users(id),
     uploaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     description TEXT
   );

   -- Composite index for entity lookup
   CREATE INDEX idx_attachments_entity ON attachments(entity_type, entity_id);

This allows attaching files to various entity types (users, alerts, locations) using the same table.

Database Constraints
==================

To maintain data integrity, several constraints are implemented:

Check Constraints
---------------

.. code-block:: sql
   :caption: Example check constraints

   -- Ensure valid email format
   ALTER TABLE users ADD CONSTRAINT valid_email 
     CHECK (email ~* '^[A-Za-z0-9._%-]+@[A-Za-z0-9.-]+[.][A-Za-z]+$');
   
   -- Ensure valid security levels
   ALTER TABLE locations ADD CONSTRAINT valid_security_level
     CHECK (requires_security_level BETWEEN 0 AND 4);
   
   -- Ensure valid alert severity
   ALTER TABLE alerts ADD CONSTRAINT valid_severity
     CHECK (severity IN ('LOW', 'MEDIUM', 'HIGH', 'CRITICAL'));

Unique Constraints
----------------

.. code-block:: sql
   :caption: Example unique constraints

   -- Ensure unique embedding per user/angle combination
   ALTER TABLE face_embeddings ADD CONSTRAINT unique_user_angle
     UNIQUE (user_id, capture_angle, embedding_version);
   
   -- Ensure unique settings keys
   ALTER TABLE settings ADD CONSTRAINT unique_setting_key
     UNIQUE (key);