Development Guide
=================

This guide covers setting up a development environment and contributing to AI Guard.

Development Environment Setup
-----------------------------

Prerequisites
~~~~~~~~~~~~~

Ensure you have the following installed:

* Python 3.8+ (3.10 recommended)
* Git for version control
* VS Code or PyCharm (recommended IDEs)
* Node.js 14+ (for frontend development)

Project Structure
~~~~~~~~~~~~~~~~~

.. code-block:: text

   ai-guard/
   ├── app.py                    # Flask application entry point
   ├── run.py                    # Development server launcher
   ├── config.py                 # Configuration management
   ├── requirements.txt          # Python dependencies
   ├── requirements-dev.txt      # Development dependencies
   ├── .env.example             # Environment variables template
   ├── README.md                # Project documentation
   │
   ├── core/                    # Core application modules
   │   ├── __init__.py
   │   ├── ai_guard_logic.py    # Facial recognition logic
   │   ├── database_manager.py  # Database operations
   │   └── auth_manager.py      # Authentication logic
   │
   ├── routes/                  # Flask route definitions
   │   ├── __init__.py
   │   ├── auth.py             # Authentication routes
   │   ├── surveillance.py     # Surveillance endpoints
   │   ├── admin.py            # Admin panel routes
   │   └── api.py              # API endpoints
   │
   ├── services/               # Business logic services
   │   ├── __init__.py
   │   ├── video_stream.py     # Video streaming service
   │   ├── alert_manager.py    # Alert processing
   │   └── websocket_manager.py # Real-time communication
   │
   ├── templates/              # Jinja2 HTML templates
   │   ├── base.html
   │   ├── auth/
   │   ├── surveillance/
   │   └── dashboard/
   │
   ├── static/                 # Static assets
   │   ├── css/
   │   ├── js/
   │   └── images/
   │
   ├── data/                   # Data storage
   │   ├── Dlib_face_recognition/  # AI models
   │   ├── snapshots/          # Captured images
   │   └── databases/          # SQLite files
   │
   ├── tests/                  # Test suite
   │   ├── __init__.py
   │   ├── test_auth.py
   │   ├── test_recognition.py
   │   └── test_api.py
   │
   └── tools/                  # Utility scripts
       ├── enroll_face.py
       ├── backup_db.py
       └── performance_test.py

Development Setup
~~~~~~~~~~~~~~~~~

**1. Clone and Setup**

.. code-block:: bash

   git clone https://github.com/your-username/ai-guard.git
   cd ai-guard
   
   # Create virtual environment
   python -m venv venv_dev
   source venv_dev/bin/activate  # or venv_dev\Scripts\activate on Windows
   
   # Install development dependencies
   pip install -r requirements-dev.txt

**2. Environment Configuration**

.. code-block:: bash

   # Copy environment template
   cp .env.example .env
   
   # Edit configuration
   nano .env

.. code-block:: ini

   # Development Configuration
   FLASK_ENV=development
   DEBUG=True
   SECRET_KEY=dev-secret-key-change-in-production
   
   # Database
   DATABASE_URL=sqlite:///data/ai_guard_dev.db
   
   # Camera
   DEFAULT_CAMERA_INDEX=0
   
   # Logging
   LOG_LEVEL=DEBUG

**3. Initialize Development Database**

.. code-block:: bash

   python -c "
   from core.database_manager import DatabaseManager
   from core.auth_manager import create_default_users
   
   print('Setting up development database...')
   db = DatabaseManager()
   create_default_users()
   print('Development environment ready!')
   "

**4. Start Development Server**

.. code-block:: bash

   python run.py

The application will be available at ``http://localhost:5000`` with hot reloading enabled.

Code Style and Standards
------------------------

Python Code Style
~~~~~~~~~~~~~~~~~~

We follow PEP 8 with some project-specific guidelines:

**Formatting Tools:**

.. code-block:: bash

   # Install code formatting tools
   pip install black flake8 isort mypy
   
   # Format code
   black .
   isort .
   
   # Check style
   flake8 .
   mypy .

**Configuration Files:**

**.flake8**

.. code-block:: ini

   [flake8]
   max-line-length = 88
   extend-ignore = E203, W503
   exclude = venv*, migrations

**pyproject.toml**

.. code-block:: toml

   [tool.black]
   line-length = 88
   target-version = ['py38']
   
   [tool.isort]
   profile = "black"
   multi_line_output = 3

JavaScript Code Style
~~~~~~~~~~~~~~~~~~~~~

For frontend JavaScript code:

.. code-block:: bash

   # Install frontend tools
   npm install -g prettier eslint
   
   # Format JavaScript
   prettier --write static/js/*.js

Testing
-------

Test Structure
~~~~~~~~~~~~~~

We use pytest for testing with the following structure:

.. code-block:: text

   tests/
   ├── conftest.py              # Test configuration
   ├── test_auth.py             # Authentication tests
   ├── test_recognition.py      # AI recognition tests
   ├── test_api.py              # API endpoint tests
   ├── test_database.py         # Database tests
   └── fixtures/                # Test data
       ├── test_images/
       └── test_database.sql

Running Tests
~~~~~~~~~~~~~

.. code-block:: bash

   # Run all tests
   pytest
   
   # Run with coverage
   pytest --cov=core --cov=services --cov=routes
   
   # Run specific test file
   pytest tests/test_auth.py
   
   # Run with verbose output
   pytest -v

Writing Tests
~~~~~~~~~~~~~

**Example Test File:**

.. code-block:: python

   # tests/test_recognition.py
   import pytest
   import cv2
   import numpy as np
   from core.ai_guard_logic import AIGuardLogic
   
   @pytest.fixture
   def ai_logic():
       """Create AI logic instance for testing."""
       return AIGuardLogic()
   
   @pytest.fixture
   def test_image():
       """Create test image."""
       return cv2.imread('tests/fixtures/test_images/person1.jpg')
   
   def test_face_detection(ai_logic, test_image):
       """Test face detection functionality."""
       faces = ai_logic.detect_faces(test_image)
       assert len(faces) > 0, "Should detect at least one face"
   
   def test_face_recognition_threshold(ai_logic, test_image):
       """Test recognition with different thresholds."""
       config = {'threshold': 0.5, 'upsample': 1}
       result = ai_logic.process_single_frame(test_image, config)
       
       assert result is not None
       assert 'faces_detected' in result

**API Testing:**

.. code-block:: python

   # tests/test_api.py
   def test_surveillance_status_endpoint(client, auth_headers):
       """Test surveillance status API."""
       response = client.get('/api/surveillance/status', 
                           headers=auth_headers)
       assert response.status_code == 200
       
       data = response.get_json()
       assert 'status' in data
       assert 'fps' in data

Database Development
--------------------

Schema Management
~~~~~~~~~~~~~~~~~

**Database Migrations:**

.. code-block:: python

   # tools/migrate_db.py
   def upgrade_database():
       """Apply database schema upgrades."""
       db = DatabaseManager()
       
       # Add new columns
       db.execute('''
           ALTER TABLE users 
           ADD COLUMN last_login TIMESTAMP
       ''')
       
       # Create new tables
       db.execute('''
           CREATE TABLE IF NOT EXISTS system_logs (
               id INTEGER PRIMARY KEY,
               timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
               level VARCHAR(10),
               message TEXT,
               module VARCHAR(50)
           )
       ''')

**Development Database Reset:**

.. code-block:: bash

   # Reset development database
   python -c "
   import os
   from core.database_manager import DatabaseManager
   
   # Remove existing database
   if os.path.exists('data/ai_guard_dev.db'):
       os.remove('data/ai_guard_dev.db')
   
   # Recreate with fresh schema
   db = DatabaseManager()
   print('Database reset complete')
   "

Frontend Development
--------------------

JavaScript Architecture
~~~~~~~~~~~~~~~~~~~~~~~~

The frontend uses vanilla JavaScript with WebSocket for real-time features:

**Main JavaScript Files:**

* ``static/js/main.js`` - Core application logic
* ``static/js/surveillance.js`` - Surveillance interface
* ``static/js/dashboard.js`` - Dashboard functionality
* ``static/js/auth.js`` - Authentication handling

**WebSocket Communication:**

.. code-block:: javascript

   // static/js/surveillance.js
   class SurveillanceManager {
       constructor() {
           this.socket = io();
           this.setupWebSocket();
       }
       
       setupWebSocket() {
           this.socket.on('new_alert', (data) => {
               this.displayAlert(data);
           });
           
           this.socket.on('system_status', (data) => {
               this.updateStatus(data);
           });
       }
       
       startSurveillance() {
           fetch('/api/surveillance/start', {
               method: 'POST',
               headers: {'Content-Type': 'application/json'},
               body: JSON.stringify({
                   camera_id: 0,
                   threshold: 0.5
               })
           }).then(response => response.json())
             .then(data => console.log('Started:', data));
       }
   }

CSS Framework
~~~~~~~~~~~~~

We use Tailwind CSS for styling:

.. code-block:: bash

   # Install Tailwind CSS
   npm install -D tailwindcss
   npx tailwindcss init
   
   # Build CSS
   npx tailwindcss -i ./static/css/input.css -o ./static/css/output.css --watch

**Custom CSS Components:**

.. code-block:: css

   /* static/css/components.css */
   .alert-box {
       @apply bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded;
   }
   
   .status-active {
       @apply bg-green-100 text-green-800 px-2 py-1 rounded-full text-sm;
   }

Performance Development
-----------------------

Profiling
~~~~~~~~~

**Python Profiling:**

.. code-block:: python

   # tools/profile_recognition.py
   import cProfile
   import time
   from core.ai_guard_logic import AIGuardLogic
   
   def profile_recognition():
       logic = AIGuardLogic()
       
       # Profile face recognition
       profiler = cProfile.Profile()
       profiler.enable()
       
       # Run recognition multiple times
       for i in range(100):
           result = logic.process_test_frame()
       
       profiler.disable()
       profiler.dump_stats('recognition_profile.prof')

**Memory Profiling:**

.. code-block:: bash

   # Install memory profiler
   pip install memory-profiler
   
   # Run with memory profiling
   python -m memory_profiler core/ai_guard_logic.py

Load Testing
~~~~~~~~~~~~

**API Load Testing:**

.. code-block:: python

   # tools/load_test.py
   import asyncio
   import aiohttp
   import time
   
   async def test_surveillance_endpoint():
       async with aiohttp.ClientSession() as session:
           tasks = []
           for i in range(100):
               task = session.get('http://localhost:5000/api/surveillance/status')
               tasks.append(task)
           
           start_time = time.time()
           responses = await asyncio.gather(*tasks)
           end_time = time.time()
           
           print(f"100 requests completed in {end_time - start_time:.2f} seconds")

Debugging
---------

Development Debugging
~~~~~~~~~~~~~~~~~~~~~

**VS Code Configuration:**

.. code-block:: json

   // .vscode/launch.json
   {
       "version": "0.2.0",
       "configurations": [
           {
               "name": "Python: Flask",
               "type": "python",
               "request": "launch",
               "program": "${workspaceFolder}/run.py",
               "env": {
                   "FLASK_ENV": "development",
                   "FLASK_DEBUG": "1"
               },
               "console": "integratedTerminal"
           }
       ]
   }

**Logging Configuration:**

.. code-block:: python

   # config.py
   import logging
   
   logging.basicConfig(
       level=logging.DEBUG,
       format='%(asctime)s [%(levelname)s] %(name)s: %(message)s',
       handlers=[
           logging.FileHandler('logs/debug.log'),
           logging.StreamHandler()
       ]
   )

Production Debugging
~~~~~~~~~~~~~~~~~~~~

**Error Tracking:**

.. code-block:: python

   # Add to app.py
   import sentry_sdk
   from sentry_sdk.integrations.flask import FlaskIntegration
   
   if not app.debug:
       sentry_sdk.init(
           dsn="your-sentry-dsn",
           integrations=[FlaskIntegration()],
           traces_sample_rate=1.0
       )

Contributing Guidelines
-----------------------

Pull Request Process
~~~~~~~~~~~~~~~~~~~~

1. **Fork the Repository**
2. **Create Feature Branch**

.. code-block:: bash

   git checkout -b feature/new-feature-name

3. **Make Changes and Test**

.. code-block:: bash

   # Run tests
   pytest
   
   # Check code style
   black . && flake8 .

4. **Commit with Descriptive Messages**

.. code-block:: bash

   git commit -m "feat: add multi-camera support
   
   - Implement camera selection in UI
   - Add camera configuration management
   - Update API endpoints for multiple cameras"

5. **Push and Create Pull Request**

Code Review Checklist
~~~~~~~~~~~~~~~~~~~~~~

- [ ] Code follows project style guidelines
- [ ] Tests are included and passing
- [ ] Documentation is updated
- [ ] No sensitive information in code
- [ ] Performance impact considered
- [ ] Security implications reviewed

Release Process
---------------

Version Management
~~~~~~~~~~~~~~~~~~

We use semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, backward compatible

**Release Steps:**

.. code-block:: bash

   # Update version
   echo "1.1.0" > VERSION
   
   # Update changelog
   # Create release tag
   git tag -a v1.1.0 -m "Release version 1.1.0"
   git push origin v1.1.0

Deployment
~~~~~~~~~~

**Production Checklist:**

- [ ] Environment variables configured
- [ ] Database migrations applied
- [ ] Static files compiled
- [ ] SSL certificates installed
- [ ] Monitoring configured
- [ ] Backup procedures tested

For deployment instructions, see the installation guide.