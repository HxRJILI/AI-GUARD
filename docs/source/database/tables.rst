======
Tables
======

AI-Guard uses a PostgreSQL database to store user information, face embeddings, access logs, and system settings.

Users Table
==========

Stores information about enrolled users.

.. code-block:: sql
   :caption: users table schema

   CREATE TABLE users (
     id UUID PRIMARY KEY,
     first_name VARCHAR(50) NOT NULL,
     last_name VARCHAR(50) NOT NULL,
     email VARCHAR(100) UNIQUE,
     role VARCHAR(20) NOT NULL,
     department VARCHAR(50),
     active BOOLEAN DEFAULT true,
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
   );

   -- Indexes
   CREATE INDEX idx_users_email ON users(email);
   CREATE INDEX idx_users_role ON users(role);
   CREATE INDEX idx_users_active ON users(active);

Face Embeddings Table
===================

Stores facial embedding vectors for enrolled users.

.. code-block:: sql
   :caption: face_embeddings table schema

   CREATE TABLE face_embeddings (
     id UUID PRIMARY KEY,
     user_id UUID NOT NULL,
     embedding_vector BYTEA NOT NULL,
     embedding_version VARCHAR(20) NOT NULL,
     quality_score FLOAT,
     capture_angle VARCHAR(20),
     created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
   );

   -- Indexes
   CREATE INDEX idx_face_embeddings_user_id ON face_embeddings(user_id);
   CREATE INDEX idx_face_embeddings_version ON face_embeddings(embedding_version);

Access Logs Table
===============

Records all access attempts, both successful and unsuccessful.

.. code-block:: sql
   :caption: access_logs table schema

   CREATE TABLE access_logs (
     id UUID PRIMARY KEY,
     user_id UUID,
     location_id UUID NOT NULL,
     timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     access_granted BOOLEAN NOT NULL,
     confidence_score FLOAT,
     capture_image_path VARCHAR(255),
     device_id VARCHAR(50),
     notes TEXT,
     CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id),
     CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL
   );

   -- Indexes
   CREATE INDEX idx_access_logs_user_id ON access_logs(user_id);
   CREATE INDEX idx_access_logs_location_id ON access_logs(location_id);
   CREATE INDEX idx_access_logs_timestamp ON access_logs(timestamp);
   CREATE INDEX idx_access_logs_access_granted ON access_logs(access_granted);

Locations Table
=============

Defines physical locations monitored by the system.

.. code-block:: sql
   :caption: locations table schema

   CREATE TABLE locations (
     id UUID PRIMARY KEY,
     name VARCHAR(100) NOT NULL,
     description TEXT,
     building VARCHAR(50),
     floor VARCHAR(20),
     requires_security_level INTEGER DEFAULT 1,
     active BOOLEAN DEFAULT true
   );

   -- Indexes
   CREATE INDEX idx_locations_building ON locations(building);
   CREATE INDEX idx_locations_security_level ON locations(requires_security_level);
   CREATE INDEX idx_locations_active ON locations(active);

Settings Table
============

Stores system-wide configuration settings.

.. code-block:: sql
   :caption: settings table schema

   CREATE TABLE settings (
     id UUID PRIMARY KEY,
     key VARCHAR(50) UNIQUE NOT NULL,
     value TEXT NOT NULL,
     description TEXT,
     updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     updated_by UUID REFERENCES users(id)
   );

   -- Indexes
   CREATE UNIQUE INDEX idx_settings_key ON settings(key);

Alerts Table
==========

Stores security alert information.

.. code-block:: sql
   :caption: alerts table schema

   CREATE TABLE alerts (
     id UUID PRIMARY KEY,
     type VARCHAR(50) NOT NULL,
     severity VARCHAR(20) NOT NULL,
     location_id UUID,
     related_log_id UUID,
     timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
     message TEXT NOT NULL,
     status VARCHAR(20) DEFAULT 'OPEN',
     acknowledged_by UUID,
     acknowledged_at TIMESTAMP,
     resolved_by UUID,
     resolved_at TIMESTAMP,
     notes TEXT,
     CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id),
     CONSTRAINT fk_related_log FOREIGN KEY (related_log_id) REFERENCES access_logs(id),
     CONSTRAINT fk_acknowledged_by FOREIGN KEY (acknowledged_by) REFERENCES users(id),
     CONSTRAINT fk_resolved_by FOREIGN KEY (resolved_by) REFERENCES users(id)
   );

   -- Indexes
   CREATE INDEX idx_alerts_type ON alerts(type);
   CREATE INDEX idx_alerts_severity ON alerts(severity);
   CREATE INDEX idx_alerts_timestamp ON alerts(timestamp);
   CREATE INDEX idx_alerts_status ON alerts(status);

Devices Table
===========

Stores information about edge devices and cameras.

.. code-block:: sql
   :caption: devices table schema

   CREATE TABLE devices (
     id UUID PRIMARY KEY,
     device_id VARCHAR(50) UNIQUE NOT NULL,
     name VARCHAR(100) NOT NULL,
     type VARCHAR(50) NOT NULL,
     location_id UUID,
     ip_address VARCHAR(45),
     mac_address VARCHAR(17),
     firmware_version VARCHAR(50),
     last_seen TIMESTAMP,
     status VARCHAR(20) DEFAULT 'ACTIVE',
     config JSONB,
     CONSTRAINT fk_location FOREIGN KEY (location_id) REFERENCES locations(id)
   );

   -- Indexes
   CREATE UNIQUE INDEX idx_devices_device_id ON devices(device_id);
   CREATE INDEX idx_devices_location_id ON devices(location_id);
   CREATE INDEX idx_devices_type ON devices(type);
   CREATE INDEX idx_devices_status ON devices(status);

Data Retention
============

To comply with data protection regulations and optimize storage, the system implements automatic data retention policies:

.. code-block:: sql
   :caption: Example retention policy function

   CREATE OR REPLACE FUNCTION cleanup_old_logs() RETURNS void AS $
   BEGIN
     -- Remove logs older than 90 days
     DELETE FROM access_logs 
     WHERE timestamp < NOW() - INTERVAL '90 days'
     AND access_granted = true;
     
     -- Keep failed attempts longer (180 days) for security analysis
     DELETE FROM access_logs 
     WHERE timestamp < NOW() - INTERVAL '180 days'
     AND access_granted = false;
     
     -- Archive access_logs data to access_logs_archive if needed
     INSERT INTO access_logs_archive
     SELECT * FROM access_logs 
     WHERE timestamp < NOW() - INTERVAL '30 days';
   END;
   $ LANGUAGE plpgsql;