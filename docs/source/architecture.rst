System Architecture
===================

AI Guard is built with a modular architecture designed for scalability, maintainability, and real-time performance.

System Overview
---------------

The system consists of several interconnected components:

.. code-block:: text

   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
   │   Web Browser   │    │   Mobile App    │    │  External API   │
   │   (Frontend)    │    │   (Optional)    │    │  Integration    │
   └─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
             │                      │                      │
             └──────────────────────┼──────────────────────┘
                                    │
   ┌─────────────────────────────────┼─────────────────────────────────┐
   │                Flask Web Application                              │
   │  ┌─────────────────┐  ┌─────────┴──────────┐  ┌─────────────────┐ │
   │  │   Auth Module   │  │   API Routes       │  │ WebSocket       │ │
   │  │                 │  │                    │  │ Manager         │ │
   │  └─────────────────┘  └────────────────────┘  └─────────────────┘ │
   └─────────────────────────────────┼─────────────────────────────────┘
                                     │
   ┌─────────────────────────────────┼─────────────────────────────────┐
   │                Core Services                                      │
   │  ┌─────────────────┐  ┌─────────┴──────────┐  ┌─────────────────┐ │
   │  │  Video Stream   │  │   Alert Manager    │  │   AI Guard      │ │
   │  │   Service       │  │                    │  │   Logic         │ │
   │  └─────────────────┘  └────────────────────┘  └─────────────────┘ │
   └─────────────────────────────────┼─────────────────────────────────┘
                                     │
   ┌─────────────────────────────────┼─────────────────────────────────┐
   │                Data Layer                                         │
   │  ┌─────────────────┐  ┌─────────┴──────────┐  ┌─────────────────┐ │
   │  │ SQLite Database │  │   Face Embeddings  │  │   File Storage  │ │
   │  │                 │  │   Database         │  │  (Snapshots)    │ │
   │  └─────────────────┘  └────────────────────┘  └─────────────────┘ │
   └─────────────────────────────────┼─────────────────────────────────┘
                                     │
   ┌─────────────────────────────────┼─────────────────────────────────┐
   │              Hardware Layer                                       │
   │  ┌─────────────────┐  ┌─────────┴──────────┐  ┌─────────────────┐ │
   │  │   USB Camera    │  │    IP Cameras      │  │   File System   │ │
   │  │                 │  │                    │  │                 │ │
   │  └─────────────────┘  └────────────────────┘  └─────────────────┘ │
   └───────────────────────────────────────────────────────────────────┘

Core Components
---------------

Flask Web Application
~~~~~~~~~~~~~~~~~~~~~

The main application server built with Flask framework:

**Key Features:**

* RESTful API endpoints
* Session-based authentication
* Role-based access control
* Template rendering for web UI
* WebSocket support for real-time communication

**Configuration:**

.. code-block:: python

   class Config:
       SECRET_KEY = os.environ.get('SECRET_KEY')
       SQLALCHEMY_DATABASE_URI = 'sqlite:///ai_guard.db'
       UPLOAD_FOLDER = 'uploads'
       MAX_CONTENT_LENGTH = 16 * 1024 * 1024  # 16MB max file size

AI Guard Logic Module
~~~~~~~~~~~~~~~~~~~~~

Core facial recognition engine using Dlib:

**Components:**

* **Face Detection**: HOG-based detector for face localization
* **Landmark Prediction**: 68-point facial landmark detection
* **Feature Extraction**: ResNet-based 128-dimensional face embeddings
* **Recognition**: Euclidean distance matching with configurable threshold

**Processing Pipeline:**

.. code-block:: python

   def process_single_frame(frame, models, config):
       # 1. Detect faces in frame
       faces = detector(gray_frame, config.upsample)
       
       # 2. Extract landmarks for each face
       landmarks = [predictor(gray_frame, face) for face in faces]
       
       # 3. Compute face embeddings
       embeddings = [encoder.compute_face_descriptor(frame, landmark) 
                     for landmark in landmarks]
       
       # 4. Match against known faces
       matches = match_faces(embeddings, known_faces, config.threshold)
       
       # 5. Generate alerts if needed
       alerts = generate_alerts(matches, config.alert_rules)
       
       return annotated_frame, alerts, face_count

Video Stream Service
~~~~~~~~~~~~~~~~~~~~

Real-time video processing and streaming:

**MJPEG Streaming:**

.. code-block:: python

   def generate_frames():
       while True:
           frame = camera.get_frame()
           processed_frame, alerts = ai_logic.process_frame(frame)
           
           # Convert to JPEG
           _, buffer = cv2.imencode('.jpg', processed_frame)
           frame_bytes = buffer.tobytes()
           
           yield (b'--frame\r\n'
                  b'Content-Type: image/jpeg\r\n\r\n' + frame_bytes + b'\r\n')

**Threading Model:**

* **Capture Thread**: Continuously captures frames from camera
* **Processing Thread**: Runs AI recognition on frames
* **Streaming Thread**: Serves processed frames to clients
* **Alert Thread**: Handles alert generation and distribution

Alert Manager
~~~~~~~~~~~~~

Real-time alert processing and distribution:

**Alert Types:**

* Unknown person detected
* Watchlist person identified
* Low confidence recognition
* System errors and warnings

**Distribution Channels:**

* WebSocket notifications to connected clients
* Database logging for audit trail
* Optional email notifications
* API webhook callbacks

Database Architecture
---------------------

The system uses SQLite for local data storage with the following schema:

User Management
~~~~~~~~~~~~~~~

.. code-block:: sql

   CREATE TABLE users (
       id INTEGER PRIMARY KEY AUTOINCREMENT,
       username VARCHAR(80) UNIQUE NOT NULL,
       password_hash VARCHAR(128) NOT NULL,
       role VARCHAR(20) NOT NULL,
       full_name VARCHAR(100),
       email VARCHAR(120),
       is_active BOOLEAN DEFAULT 1,
       created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       last_login TIMESTAMP
   );

Face Recognition Data
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

   CREATE TABLE authorized_faces (
       id INTEGER PRIMARY KEY AUTOINCREMENT,
       name VARCHAR(100) NOT NULL,
       role VARCHAR(50),
       student_id VARCHAR(20),
       embedding BLOB NOT NULL,  -- 128-dimensional float array
       enrollment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       created_by INTEGER REFERENCES users(id),
       is_active BOOLEAN DEFAULT 1,
       image_path VARCHAR(255)
   );

Alert History
~~~~~~~~~~~~~

.. code-block:: sql

   CREATE TABLE alert_history (
       id INTEGER PRIMARY KEY AUTOINCREMENT,
       timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       alert_type VARCHAR(50) NOT NULL,
       person_name VARCHAR(100),
       confidence_score REAL,
       camera_id INTEGER,
       snapshot_path VARCHAR(255),
       handled_by INTEGER REFERENCES users(id),
       resolution_status VARCHAR(20) DEFAULT 'pending',
       notes TEXT
   );

System Configuration
~~~~~~~~~~~~~~~~~~~~

.. code-block:: sql

   CREATE TABLE system_config (
       key VARCHAR(50) PRIMARY KEY,
       value TEXT,
       description TEXT,
       updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
       updated_by INTEGER REFERENCES users(id)
   );

Security Architecture
---------------------

Authentication & Authorization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Multi-layered Security:**

1. **Session Management**: Flask-Login for session handling
2. **Password Security**: bcrypt hashing with salt
3. **Role-Based Access**: Decorator-based permission checking
4. **API Security**: Token-based authentication for API access

**Permission Matrix:**

.. list-table::
   :header-rows: 1
   :widths: 20 20 20 20 20

   * - Feature
     - Admin
     - Surveillant
     - Guard
     - Service Admin
   * - Live Monitoring
     - ✓
     - ✓
     - ✓
     - ✗
   * - User Management
     - ✓
     - ✗
     - ✗
     - ✗
   * - System Config
     - ✓
     - Limited
     - ✗
     - ✗
   * - Alert Management
     - ✓
     - ✓
     - View Only
     - ✗
   * - Face Enrollment
     - ✓
     - ✓
     - ✗
     - ✓

Data Protection
~~~~~~~~~~~~~~~

**Privacy Measures:**

* Face embeddings stored instead of raw images
* Automatic cleanup of temporary files
* Configurable data retention policies
* Secure communication protocols (HTTPS)
* Local data storage (no cloud transmission)

Performance Optimization
------------------------

Real-time Processing
~~~~~~~~~~~~~~~~~~~~

**Optimization Strategies:**

* **Frame Skipping**: Process every Nth frame to maintain real-time performance
* **Resolution Scaling**: Reduce image size for faster processing
* **Model Caching**: Keep AI models loaded in memory
* **Threading**: Separate capture, processing, and streaming threads

**Performance Monitoring:**

.. code-block:: python

   class PerformanceMonitor:
       def __init__(self):
           self.fps_counter = FPSCounter()
           self.processing_times = deque(maxlen=100)
           self.memory_usage = psutil.Process().memory_info()
       
       def log_frame_processing(self, start_time, end_time):
           processing_time = end_time - start_time
           self.processing_times.append(processing_time)
           self.fps_counter.update()

Memory Management
~~~~~~~~~~~~~~~~~

**Automatic Cleanup:**

* Regular garbage collection
* Temporary file cleanup
* Database connection pooling
* Memory-mapped file access for large models

**Resource Limits:**

.. code-block:: python

   # Memory limits
   MAX_QUEUE_SIZE = 30  # Maximum frames in processing queue
   MAX_ALERT_HISTORY = 1000  # Maximum alerts kept in memory
   CLEANUP_INTERVAL = 300  # Cleanup every 5 minutes

Scalability Considerations
--------------------------

Horizontal Scaling
~~~~~~~~~~~~~~~~~~

**Multi-Camera Support:**

* Each camera runs in separate process
* Centralized alert aggregation
* Load balancing across processing nodes
* Shared database for coordination

**Distributed Deployment:**

.. code-block:: text

   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
   │   Camera Node   │    │   Camera Node   │    │   Camera Node   │
   │   (Building A)  │    │   (Building B)  │    │   (Building C)  │
   └─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
             │                      │                      │
             └──────────────────────┼──────────────────────┘
                                    │
   ┌─────────────────────────────────┼─────────────────────────────────┐
   │                Central Management Server                          │
   │  ┌─────────────────┐  ┌─────────┴──────────┐  ┌─────────────────┐ │
   │  │   Alert         │  │   User Management  │  │   Reporting     │ │
   │  │   Aggregation   │  │                    │  │   Dashboard     │ │
   │  └─────────────────┘  └────────────────────┘  └─────────────────┘ │
   └───────────────────────────────────────────────────────────────────┘

Deployment Architecture
-----------------------

Development Environment
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   ┌─────────────────────────────────────────────────────────────┐
   │                Development Setup                            │
   │                                                             │
   │  ┌─────────────────┐    ┌─────────────────┐                │
   │  │   VS Code       │    │   Local Browser │                │
   │  │   - Python      │    │   localhost:5000│                │
   │  │   - Debugging   │    │                 │                │
   │  └─────────────────┘    └─────────────────┘                │
   │                                                             │
   │  ┌─────────────────┐    ┌─────────────────┐                │
   │  │  Virtual Env    │    │   SQLite DB     │                │
   │  │  - Dependencies │    │   - Local file  │                │
   │  │  - Isolation    │    │   - Development │                │
   │  └─────────────────┘    └─────────────────┘                │
   └─────────────────────────────────────────────────────────────┘

Production Environment
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   ┌─────────────────────────────────────────────────────────────┐
   │                Production Setup                             │
   │                                                             │
   │  ┌─────────────────┐    ┌─────────────────┐                │
   │  │   Nginx         │    │   Load Balancer │                │
   │  │   - SSL Term    │    │   - Multi-node  │                │
   │  │   - Static      │    │   - Health Check│                │
   │  └─────────────────┘    └─────────────────┘                │
   │                                                             │
   │  ┌─────────────────┐    ┌─────────────────┐                │
   │  │   Gunicorn      │    │   PostgreSQL    │                │
   │  │   - WSGI Server │    │   - Production  │                │
   │  │   - Multi-proc  │    │   - Backup      │                │
   │  └─────────────────┘    └─────────────────┘                │
   └─────────────────────────────────────────────────────────────┘

For detailed deployment instructions, see the :doc:`development` section.