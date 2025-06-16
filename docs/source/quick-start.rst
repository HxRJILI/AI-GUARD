Quick Start Guide
=================

Get AI Guard running in 5 minutes with this quick start guide.

Launch AI Guard
---------------

1. **Activate Environment**

.. code-block:: bash

   # Navigate to project directory
   cd ai-guard
   
   # Activate virtual environment
   source venv_ai_guard/bin/activate  # or venv_ai_guard\Scripts\activate on Windows

2. **Start the Application**

.. code-block:: bash

   python run.py

You should see:

.. code-block:: text

   AI Guard - Intelligent Surveillance System
   ==========================================
   Environment: development
   Web Interface: http://localhost:5000
   Test Accounts:
   - Admin: admin / admin123
   - Surveillant: surveillant1 / surv123
   - Guard: garde1 / garde123

3. **Access Web Interface**

Open your browser and navigate to: ``http://localhost:5000``

First Login
-----------

Use one of the default accounts to log in:

**Administrator Account**
   * Username: ``admin``
   * Password: ``admin123``
   * Access: Complete system control

**Surveillance Account**
   * Username: ``surveillant1``
   * Password: ``surv123``
   * Access: Monitoring and reporting

**Security Guard Account**
   * Username: ``garde1``
   * Password: ``garde123``
   * Access: Basic monitoring

Basic Operation
---------------

1. **Start Surveillance**

   * Log in with any account
   * Navigate to "Surveillance > Live"
   * Click "Start Monitoring"
   * Allow camera access when prompted

2. **Configure Recognition**

   * Adjust recognition threshold (0.3-0.7)
   * Set detection upsample level (0-2)
   * Configure frame processing interval

3. **Monitor Alerts**

   * View real-time alerts in the sidebar
   * Check alert history in the dashboard
   * Configure watchlist for specific individuals

Adding Authorized Faces
-----------------------

To add new authorized personnel:

1. **Prepare Face Images**

   * Use clear, front-facing photos
   * Ensure good lighting
   * Save as JPG or PNG format

2. **Enroll Faces** (via Python script)

.. code-block:: bash

   python tools/enroll_face.py --name "John Doe" --photo "photos/john.jpg" --role "student"

3. **Verify Enrollment**

   * Check the authorized faces database
   * Test recognition with live camera
   * Adjust threshold if needed

Default Configuration
---------------------

The system starts with these default settings:

* **Recognition Threshold**: 0.5 (50% confidence)
* **Detection Upsample**: 1 (balanced speed/accuracy)
* **Frame Processing**: Every 3 frames
* **Alert Cooldown**: 30 seconds
* **Camera Resolution**: 640x480

Next Steps
----------

* Read the complete :doc:`user-guide` for detailed functionality
* Configure :doc:`api-reference` for integrations
* Check :doc:`development` for customization options

Need Help?
----------

* See :doc:`troubleshooting` for common issues
* Check the GitHub repository for latest updates
* Review system logs for error messages