Troubleshooting Guide
=====================

Common issues and solutions for AI Guard installation and operation.

Installation Issues
-------------------

Dlib Installation Problems
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Error: "Could not build wheels for dlib"**

This is the most common installation issue. Try these solutions:

**Solution 1: Install cmake first**

.. code-block:: bash

   pip install cmake
   pip install dlib

**Solution 2: Use conda (if available)**

.. code-block:: bash

   conda install -c conda-forge dlib

**Solution 3: Install build tools**

*Windows:*

.. code-block:: bash

   # Install Visual Studio Build Tools
   # Download from: https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2019

*macOS:*

.. code-block:: bash

   xcode-select --install

*Ubuntu/Debian:*

.. code-block:: bash

   sudo apt-get update
   sudo apt-get install build-essential cmake
   sudo apt-get install libopenblas-dev liblapack-dev
   sudo apt-get install libx11-dev libgtk-3-dev

OpenCV Issues
~~~~~~~~~~~~~

**Error: "cv2 module not found"**

.. code-block:: bash

   # Uninstall any existing opencv packages
   pip uninstall opencv-python opencv-contrib-python
   
   # Install the correct version
   pip install opencv-python

**Error: "Camera not accessible"**

.. code-block:: bash

   # Test camera access
   python -c "
   import cv2
   cap = cv2.VideoCapture(0)
   if cap.isOpened():
       print('Camera OK')
       cap.release()
   else:
       print('Camera not accessible')
   "

NumPy Compatibility
~~~~~~~~~~~~~~~~~~~

**Error: "numpy version conflict"**

.. code-block:: bash

   # Uninstall and reinstall numpy
   pip uninstall numpy
   pip install "numpy>=1.19.0,<2.0.0"

**TensorFlow Compatibility Issues:**

.. code-block:: bash

   # Install compatible versions
   pip install "numpy>=1.19.0,<1.24.0"
   pip install "tensorflow>=2.8.0,<2.12.0"

Runtime Issues
--------------

Camera Problems
~~~~~~~~~~~~~~~

**Camera Not Detected**

*Check camera index:*

.. code-block:: python

   import cv2
   
   # Test different camera indices
   for i in range(5):
       cap = cv2.VideoCapture(i)
       if cap.isOpened():
           print(f"Camera {i}: Available")
           cap.release()
       else:
           print(f"Camera {i}: Not available")

*Update .env file:*

.. code-block:: ini

   DEFAULT_CAMERA_INDEX=1  # Change to working camera index

**Camera Access Permissions**

*Windows:*

1. Go to Settings > Privacy > Camera
2. Enable "Allow apps to access your camera"
3. Enable "Allow desktop apps to access your camera"

*macOS:*

1. System Preferences > Security & Privacy > Camera
2. Enable camera access for Terminal or your IDE

*Linux:*

.. code-block:: bash

   # Add user to video group
   sudo usermod -a -G video $USER
   
   # Check camera permissions
   ls -l /dev/video*

**Camera Already in Use**

.. code-block:: bash

   # Find processes using camera (Linux/macOS)
   lsof /dev/video0
   
   # Kill processes if needed
   sudo pkill -f "python.*camera"

Performance Issues
~~~~~~~~~~~~~~~~~~

**High CPU Usage**

*Reduce processing load:*

.. code-block:: python

   # In web interface, adjust these settings:
   # - Increase frame skip interval (process every 3-5 frames)
   # - Reduce detection upsample (set to 0)
   # - Lower camera resolution

*Monitor performance:*

.. code-block:: bash

   # Check system resources
   htop  # or Task Manager on Windows
   
   # Monitor AI Guard specifically
   python -c "
   import psutil
   import time
   
   while True:
       cpu = psutil.cpu_percent()
       memory = psutil.virtual_memory().percent
       print(f'CPU: {cpu}%, Memory: {memory}%')
       time.sleep(1)
   "

**Low Frame Rate**

*Optimize settings:*

.. code-block:: ini

   # In .env file
   FRAME_SKIP_INTERVAL=3
   DETECTION_UPSAMPLE=0
   CAMERA_RESOLUTION_WIDTH=640
   CAMERA_RESOLUTION_HEIGHT=480

**Memory Issues**

*Check memory usage:*

.. code-block:: python

   import psutil
   
   process = psutil.Process()
   memory_info = process.memory_info()
   print(f"Memory usage: {memory_info.rss / 1024 / 1024:.2f} MB")

*Reduce memory usage:*

.. code-block:: bash

   # Restart the application periodically
   # Reduce queue sizes in configuration
   # Clear browser cache

Recognition Issues
~~~~~~~~~~~~~~~~~~

**Poor Recognition Accuracy**

*Adjust recognition threshold:*

.. code-block:: python

   # Lower threshold for more sensitive recognition
   recognition_threshold = 0.4  # Default: 0.5
   
   # Higher threshold for more strict recognition
   recognition_threshold = 0.6

*Improve image quality:*

- Ensure good lighting conditions
- Position camera at eye level
- Clean camera lens
- Minimize camera shake

**False Positives**

*Increase recognition threshold:*

.. code-block:: ini

   # In web interface or .env
   DEFAULT_RECOGNITION_THRESHOLD=0.6

*Re-enroll problematic faces:*

.. code-block:: bash

   # Delete and re-enroll with better images
   python tools/manage_faces.py --delete "Person Name"
   python tools/enroll_face.py --name "Person Name" --photo "new_photo.jpg"

**Faces Not Detected**

*Adjust detection parameters:*

.. code-block:: python

   # Increase upsample for better detection
   detection_upsample = 2  # Default: 1
   
   # Ensure minimum face size requirements
   min_face_size = 30  # pixels

*Check image conditions:*

- Adequate lighting
- Face clearly visible
- Minimal occlusion (no masks, sunglasses)
- Camera positioned properly

Database Issues
---------------

Database Corruption
~~~~~~~~~~~~~~~~~~~~

**Backup and Restore**

.. code-block:: bash

   # Create backup
   python -c "
   import shutil
   import datetime
   
   timestamp = datetime.datetime.now().strftime('%Y%m%d_%H%M%S')
   shutil.copy('data/ai_guard.db', f'data/backup_ai_guard_{timestamp}.db')
   print('Backup created')
   "

**Reset Database**

.. code-block:: bash

   # WARNING: This will delete all data
   python -c "
   import os
   from core.database_manager import DatabaseManager
   from core.auth_manager import create_default_users
   
   # Remove corrupted database
   if os.path.exists('data/ai_guard.db'):
       os.remove('data/ai_guard.db')
   
   # Recreate database
   db = DatabaseManager()
   create_default_users()
   print('Database reset complete')
   "

**Verify Database Integrity**

.. code-block:: bash

   sqlite3 data/ai_guard.db "PRAGMA integrity_check;"

Connection Issues
~~~~~~~~~~~~~~~~~

**Database Locked Error**

.. code-block:: python

   # Check for hanging connections
   import sqlite3
   import time
   
   try:
       conn = sqlite3.connect('data/ai_guard.db', timeout=30)
       conn.execute('SELECT 1')
       conn.close()
       print('Database accessible')
   except sqlite3.OperationalError as e:
       print(f'Database error: {e}')

**Permission Issues**

.. code-block:: bash

   # Fix file permissions
   chmod 664 data/ai_guard.db
   chmod 755 data/

Network Issues
--------------

WebSocket Connection Problems
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Connection Refused**

*Check firewall settings:*

.. code-block:: bash

   # Allow port 5000 through firewall
   # Windows: Windows Defender Firewall
   # macOS: System Preferences > Security & Privacy > Firewall
   # Linux: ufw allow 5000

*Test WebSocket connection:*

.. code-block:: javascript

   // In browser console
   const socket = io('http://localhost:5000');
   socket.on('connect', () => console.log('Connected'));
   socket.on('disconnect', () => console.log('Disconnected'));

**Real-time Updates Not Working**

*Check browser compatibility:*

- Use modern browser (Chrome, Firefox, Edge)
- Enable JavaScript
- Disable ad blockers
- Clear browser cache

*Test with curl:*

.. code-block:: bash

   curl -X GET http://localhost:5000/api/surveillance/status

HTTPS/SSL Issues
~~~~~~~~~~~~~~~~

**Mixed Content Errors**

*For development, use HTTP:*

.. code-block:: ini

   # In .env
   FORCE_HTTPS=False

*For production, ensure all resources use HTTPS:*

.. code-block:: python

   # In config.py
   PREFERRED_URL_SCHEME = 'https'
   SESSION_COOKIE_SECURE = True

Authentication Issues
---------------------

Login Problems
~~~~~~~~~~~~~~

**Invalid Credentials**

*Reset admin password:*

.. code-block:: bash

   python -c "
   from core.auth_manager import reset_admin_password
   reset_admin_password('new_password_123')
   print('Admin password reset')
   "

**Session Timeout**

*Extend session duration:*

.. code-block:: python

   # In config.py
   PERMANENT_SESSION_LIFETIME = 3600  # 1 hour in seconds

Permission Denied
~~~~~~~~~~~~~~~~~

**Check user roles:**

.. code-block:: bash

   python -c "
   from core.database_manager import DatabaseManager
   
   db = DatabaseManager()
   users = db.get_all_users()
   for user in users:
       print(f'{user.username}: {user.role}')
   "

**Update user permissions:**

.. code-block:: bash

   python -c "
   from core.auth_manager import update_user_role
   update_user_role('username', 'admin')
   print('User role updated')
   "

Log Analysis
------------

Enable Detailed Logging
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # Add to config.py
   import logging
   
   logging.basicConfig(
       level=logging.DEBUG,
       format='%(asctime)s [%(levelname)s] %(name)s: %(message)s',
       handlers=[
           logging.FileHandler('logs/ai_guard.log'),
           logging.StreamHandler()
       ]
   )

Common Log Messages
~~~~~~~~~~~~~~~~~~~

**Face Recognition Errors:**

.. code-block:: text

   ERROR: Face encoding failed - Check image quality
   WARNING: Low confidence match (0.3) - Consider adjusting threshold
   INFO: Face detected but not recognized - Unknown person

**Camera Errors:**

.. code-block:: text

   ERROR: Camera initialization failed - Check camera connection
   WARNING: Frame capture timeout - Camera may be busy
   INFO: Camera resolution changed - Performance may be affected

**Database Errors:**

.. code-block:: text

   ERROR: Database connection failed - Check file permissions
   WARNING: Slow query detected - Consider database optimization
   INFO: Database backup completed successfully

Getting Help
------------

Before Seeking Help
~~~~~~~~~~~~~~~~~~~~

1. **Check this troubleshooting guide**
2. **Review log files** for error messages
3. **Test with minimal configuration**
4. **Verify system requirements**

Reporting Issues
~~~~~~~~~~~~~~~~

When reporting issues, include:

**System Information:**

.. code-block:: bash

   python -c "
   import platform
   import cv2
   import dlib
   print(f'OS: {platform.system()} {platform.release()}')
   print(f'Python: {platform.python_version()}')
   print(f'OpenCV: {cv2.__version__}')
   print(f'Dlib: {dlib.version}')
   "

**Error Logs:**

.. code-block:: bash

   # Include relevant log entries
   tail -n 50 logs/ai_guard.log

**Steps to Reproduce:**

1. Exact steps taken before error
2. Expected behavior
3. Actual behavior
4. Screenshots if applicable

**Configuration:**

- Camera type and model
- Operating system version
- Python environment details
- Any custom configuration changes

Support Channels
~~~~~~~~~~~~~~~~

- **GitHub Issues**: For bugs and feature requests
- **Documentation**: Check latest documentation
- **Community Forum**: For usage questions
- **Email Support**: For security or private issues

Emergency Recovery
------------------

Complete System Reset
~~~~~~~~~~~~~~~~~~~~~

If AI Guard is completely non-functional:

.. code-block:: bash

   # Stop all AI Guard processes
   pkill -f ai_guard
   
   # Reset database
   rm data/ai_guard.db
   
   # Clear logs
   rm logs/*.log
   
   # Reinstall dependencies
   pip install -r requirements.txt --force-reinstall
   
   # Reinitialize system
   python -c "
   from core.database_manager import DatabaseManager
   from core.auth_manager import create_default_users
   
   db = DatabaseManager()
   create_default_users()
   print('System reset complete')
   "

Backup Recovery
~~~~~~~~~~~~~~~

.. code-block:: bash

   # Restore from backup
   cp data/backup_ai_guard_YYYYMMDD_HHMMSS.db data/ai_guard.db
   
   # Verify integrity
   sqlite3 data/ai_guard.db "PRAGMA integrity_check;"
   
   # Restart application
   python run.py