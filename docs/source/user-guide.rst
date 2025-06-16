User Guide
==========

Complete guide to using AI Guard's features and interfaces.

User Roles and Permissions
---------------------------

AI Guard implements role-based access control with four distinct user types:

Administrator
~~~~~~~~~~~~~

**Full System Access**

* User management (create, modify, delete accounts)
* System configuration and settings
* Complete monitoring and reporting access
* Database management and maintenance
* Alert configuration and management

**Dashboard Features:**

* Real-time system metrics
* User activity monitoring
* System health indicators
* Complete alert history
* Advanced reporting tools

Surveillant (Supervisor)
~~~~~~~~~~~~~~~~~~~~~~~~

**Advanced Monitoring**

* Real-time surveillance monitoring
* Alert management and response
* Incident reporting and documentation
* User profile viewing (limited)
* Historical data access

**Dashboard Features:**

* Live monitoring interface
* Alert management panel
* Incident reporting tools
* Surveillance statistics
* Shift management

Security Guard
~~~~~~~~~~~~~~

**Basic Monitoring**

* Live surveillance access
* Basic alert viewing
* Snapshot capture
* Simple reporting

**Dashboard Features:**

* Simplified monitoring interface
* Current shift alerts
* Basic statistics view
* Personal activity log

Service Administrator
~~~~~~~~~~~~~~~~~~~~~

**Administrative Support**

* Student enrollment validation
* Profile data management
* Communication tools
* Limited reporting access

Surveillance Interface
----------------------

Live Monitoring
~~~~~~~~~~~~~~~

The live monitoring interface provides real-time facial recognition capabilities:

**Main Video Display**

* Live camera feed with recognition overlays
* Detected faces highlighted with bounding boxes
* Recognition confidence scores
* Person identification labels

**Control Panel**

* Start/Stop monitoring toggle
* Camera selection (if multiple cameras)
* Recording controls
* Snapshot capture

**Recognition Settings**

* **Recognition Threshold**: Confidence level required for positive identification (0.3-0.7)
* **Detection Upsample**: Image upsampling for better detection (0-2)
* **Processing Interval**: Frames between recognition attempts (1-5)
* **Alert Cooldown**: Minimum time between alerts for same person (10-300 seconds)

**Alert Configuration**

* **Watchlist Mode**: Monitor specific individuals
* **Unknown Person Alerts**: Alert on unrecognized faces
* **Confidence Alerts**: Alert on low-confidence matches
* **Custom Alert Rules**: Define specific monitoring criteria

Real-time Alerts
~~~~~~~~~~~~~~~~~

AI Guard provides instant alerts through multiple channels:

**Alert Types**

* **Unknown Person**: Unrecognized individual detected
* **Watchlist Match**: Person of interest identified
* **Low Confidence**: Uncertain recognition result
* **System Alert**: Technical issues or maintenance needs

**Alert Information**

* Timestamp and location
* Snapshot of detected person
* Confidence score
* Alert priority level
* Recommended actions

Dashboard Features
------------------

Administrator Dashboard
~~~~~~~~~~~~~~~~~~~~~~~

**System Overview**

* Current active users
* System performance metrics
* Recent alerts summary
* Camera status indicators

**User Management**

* Create new user accounts
* Modify existing permissions
* View user activity logs
* Manage account status

**System Configuration**

* Recognition model settings
* Alert thresholds and rules
* Camera configuration
* Database maintenance

**Reporting Tools**

* Generate custom reports
* Export surveillance data
* Schedule automated reports
* Statistical analysis

Surveillant Dashboard
~~~~~~~~~~~~~~~~~~~~~

**Monitoring Overview**

* Active surveillance status
* Current shift statistics
* Recent alert summary
* Camera feed thumbnails

**Alert Management**

* Review and acknowledge alerts
* Create incident reports
* Escalate critical situations
* Track alert resolution

**Reporting Interface**

* Generate shift reports
* Document incidents
* Export surveillance logs
* Statistical summaries

Guard Dashboard
~~~~~~~~~~~~~~~

**Simplified Interface**

* Current monitoring status
* Today's alert count
* Shift duration timer
* Basic statistics

**Quick Actions**

* Start/stop surveillance
* Capture manual snapshots
* Report incidents
* View recent alerts

Database Management
-------------------

Authorized Faces Database
~~~~~~~~~~~~~~~~~~~~~~~~~~

The system maintains a SQLite database of authorized personnel:

**Face Enrollment**

* Store facial embeddings (128-dimensional vectors)
* Associate with person metadata (name, role, ID)
* Track enrollment date and source
* Maintain version history

**Data Structure**

.. code-block:: sql

   CREATE TABLE authorized_faces (
       id INTEGER PRIMARY KEY,
       name TEXT NOT NULL,
       role TEXT,
       student_id TEXT,
       embedding BLOB NOT NULL,
       enrollment_date TIMESTAMP,
       created_by TEXT,
       is_active BOOLEAN DEFAULT 1
   );

**Maintenance Operations**

* Regular database optimization
* Backup and restore procedures
* Data integrity verification
* Performance monitoring

Alert History
~~~~~~~~~~~~~

Complete logging of all surveillance activities:

**Alert Records**

.. code-block:: sql

   CREATE TABLE alert_history (
       id INTEGER PRIMARY KEY,
       timestamp TIMESTAMP,
       alert_type TEXT,
       person_name TEXT,
       confidence_score REAL,
       camera_id INTEGER,
       snapshot_path TEXT,
       handled_by TEXT,
       resolution_status TEXT
   );

**Data Retention**

* Configurable retention periods
* Automatic archiving of old records
* Export capabilities for compliance
* Privacy-compliant data handling

System Configuration
--------------------

Recognition Parameters
~~~~~~~~~~~~~~~~~~~~~~

**Threshold Settings**

* **Recognition Threshold**: 0.3-0.7 (default: 0.5)
  
  * Lower values: More sensitive, may increase false positives
  * Higher values: More strict, may miss valid matches

* **Detection Confidence**: Minimum confidence for face detection
* **Batch Processing**: Number of frames to process simultaneously

**Performance Optimization**

* **Frame Skip**: Process every N frames for performance
* **Resolution Scaling**: Reduce image size for faster processing
* **Multi-threading**: Enable parallel processing
* **Memory Management**: Automatic cleanup and optimization

Camera Configuration
~~~~~~~~~~~~~~~~~~~~

**Camera Settings**

* Resolution and frame rate
* Exposure and brightness adjustment
* Focus and zoom controls
* Camera positioning guidelines

**Multiple Camera Support**

* Camera numbering and identification
* Concurrent monitoring capability
* Load balancing across cameras
* Failover and redundancy

Security Settings
~~~~~~~~~~~~~~~~~

**Access Control**

* Session timeout configuration
* Password complexity requirements
* Two-factor authentication options
* API access controls

**Data Protection**

* Encryption for sensitive data
* Secure communication protocols
* Regular security updates
* Compliance with privacy regulations

Troubleshooting Common Issues
-----------------------------

For detailed troubleshooting information, see :doc:`troubleshooting`.