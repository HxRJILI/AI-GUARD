Installation Guide
==================

This guide covers the complete installation process for AI Guard.

Prerequisites
-------------

System Requirements
~~~~~~~~~~~~~~~~~~~

* **Operating System**: Windows 10/11, macOS 10.15+, or Ubuntu 18.04+
* **Python**: Version 3.8 or higher (3.10 recommended)
* **Memory**: 8GB RAM minimum, 16GB recommended
* **Storage**: 5GB free space
* **Camera**: USB webcam or IP camera
* **Browser**: Chrome, Firefox, or Edge

Required Software
~~~~~~~~~~~~~~~~~

Before installing AI Guard, ensure you have:

* Python 3.8+ with pip
* Git (for source code management)
* C++ compiler (for Dlib compilation)

Installation Steps
------------------

1. Clone the Repository
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   git clone https://github.com/your-username/ai-guard.git
   cd ai-guard

2. Create Virtual Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   python -m venv venv_ai_guard
   
   # Windows
   venv_ai_guard\Scripts\activate
   
   # macOS/Linux
   source venv_ai_guard/bin/activate

3. Install Dependencies
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   pip install --upgrade pip
   pip install -r requirements.txt

4. Download Dlib Models
~~~~~~~~~~~~~~~~~~~~~~~

Download the required facial recognition models:

.. code-block:: bash

   # Create models directory
   mkdir -p data/Dlib_face_recognition
   cd data/Dlib_face_recognition
   
   # Download facial landmarks predictor (99MB)
   wget http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2
   bunzip2 shape_predictor_68_face_landmarks.dat.bz2
   
   # Download face recognition model (22MB)
   wget http://dlib.net/files/dlib_face_recognition_resnet_model_v1.dat.bz2
   bunzip2 dlib_face_recognition_resnet_model_v1.dat.bz2

5. Initialize Database
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: bash

   python -c "
   from core.database_manager import DatabaseManager
   from core.auth_manager import create_default_users
   
   db = DatabaseManager()
   create_default_users()
   print('Database initialized successfully')
   "

6. Configuration
~~~~~~~~~~~~~~~~

Copy the example configuration file:

.. code-block:: bash

   cp .env.example .env

Edit the ``.env`` file with your specific settings:

.. code-block:: ini

   # Camera Configuration
   DEFAULT_CAMERA_INDEX=0
   
   # Recognition Settings
   DEFAULT_RECOGNITION_THRESHOLD=0.5
   DEFAULT_DETECTION_UPSAMPLE=1
   
   # Security
   SECRET_KEY=your-secret-key-here
   
   # Development Settings
   DEBUG=True
   FLASK_ENV=development

Verification
------------

Test your installation:

.. code-block:: bash

   # Test dependencies
   python -c "
   import cv2, dlib, numpy, flask
   print('All dependencies installed successfully')
   "
   
   # Test camera
   python -c "
   import cv2
   cap = cv2.VideoCapture(0)
   if cap.isOpened():
       print('Camera detected successfully')
       cap.release()
   else:
       print('No camera detected')
   "

Troubleshooting Installation
----------------------------

Common Issues
~~~~~~~~~~~~~

**Dlib Installation Failed**

.. code-block:: bash

   # Install cmake first
   pip install cmake
   pip install dlib

**Camera Not Detected**

* Ensure your camera is connected and not used by other applications
* Try different camera indices (0, 1, 2) in configuration
* Check camera permissions on your operating system

**Permission Errors**

.. code-block:: bash

   # Use virtual environment
   python -m venv venv_ai_guard
   source venv_ai_guard/bin/activate  # or activate.bat on Windows

For additional troubleshooting, see :doc:`troubleshooting`.