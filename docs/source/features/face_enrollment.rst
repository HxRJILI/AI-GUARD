===============
Face Enrollment
===============

Face enrollment is the process of registering authorized users in the system. The process captures multiple facial angles to ensure robust recognition under various conditions.

Enrollment Workflow
=================

1. **User Authentication**: Administrator or enrollment officer logs into the system
2. **Basic Information Entry**: Enter the user's name, ID, and access level
3. **Face Capture**: The system guides the user through capturing:
   - Front-facing neutral expression
   - Left and right profile views (45° angles)
   - Slight up and down angles
   - With and without common accessories (glasses if applicable)
4. **Quality Check**: System verifies image quality and facial landmark detection
5. **Embedding Generation**: System processes images to create facial embeddings
6. **Verification Test**: Quick recognition test to confirm successful enrollment
7. **Confirmation**: System confirms successful enrollment and access level

.. figure:: /_static/enrollment-workflow.png
   :alt: Face Enrollment Workflow
   :align: center
   
   Face Enrollment Process Flow

Multi-angle capture logic
=======================

The system employs on-screen guides that direct the user to position their face properly:

- Visual indicators show the desired face position
- Real-time feedback indicates when the correct angle is achieved
- Quality metrics ensure adequate lighting and resolution
- Automatic capture occurs when optimal conditions are met

.. code-block:: javascript
   :caption: Quality Assessment Logic
   
   function assessQuality(image) {
     const brightness = calculateBrightness(image);
     const contrast = calculateContrast(image);
     const sharpness = calculateSharpness(image);
     const faceSize = detectFaceSize(image);
     
     return {
       score: (brightness * 0.3 + contrast * 0.2 + sharpness * 0.3 + faceSize * 0.2),
       metrics: { brightness, contrast, sharpness, faceSize },
       threshold: 0.7
     };
   }

Image quality factors
===================

Several factors are measured to ensure optimal image quality for reliable recognition:

1. **Brightness**: Well-lit face without overexposure
2. **Contrast**: Good distinction between facial features
3. **Sharpness**: Clear, in-focus image without motion blur
4. **Face Size**: Face occupies sufficient portion of the frame
5. **Head Angle**: Correct orientation for the specific capture
6. **Occlusion**: No significant coverage of facial features

Enrollment Management
===================

Administrators can manage the enrollment database:

- Update user information
- Require re-enrollment after a specified period
- Temporarily deactivate enrollments
- Schedule batch enrollments for large groups
- Export enrollment statistics

Best Practices
============

- **Consistent Lighting**: Perform enrollment in well-lit conditions similar to deployment areas
- **Multiple Sessions**: Consider enrolling users on different days for appearance variations
- **Accessory Variations**: Include common accessories like glasses or light makeup if regularly worn
- **Clear Instructions**: Provide users with clear guidance on positioning and expectations
- **Privacy Consent**: Always obtain explicit consent before enrollment