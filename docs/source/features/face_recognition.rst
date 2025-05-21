===================
Face Recognition
===================

The core functionality of AI-GUARD is its ability to recognize faces in real-time from video streams.

Recognition Process
==================

.. figure:: /_static/recognition-process.png
   :alt: Face Recognition Process
   :align: center
   
   Face Recognition Pipeline

The face recognition process follows these steps:

1. **Detection**: MTCNN identifies and localizes faces in the video frame
2. **Alignment**: Detected faces are aligned based on facial landmarks
3. **Embedding Generation**: Normalized face images are processed through the FaceNet/ArcFace model to generate 128-dimensional embeddings
4. **Comparison**: Generated embeddings are compared against the database of authorized users using cosine similarity
5. **Threshold Evaluation**: Similarity scores are compared against configurable thresholds
6. **Decision**: Access granted or denied based on threshold and policy rules

Threshold Logic
=============

The system uses a configurable similarity threshold that balances security with usability:

.. list-table::
   :header-rows: 1
   :widths: 20 20 60

   * - Security Level
     - Threshold Range
     - Description
   * - **High**
     - 0.85+
     - More secure but may increase false negatives
   * - **Medium**
     - 0.75-0.85
     - Balanced for most environments
   * - **Low**
     - 0.65-0.75
     - Higher accessibility but may increase false positives

Different thresholds can be set for different security zones or time periods.

.. code-block:: python
   :caption: Example threshold configuration

   security_zones = {
       "server_room": {
           "threshold": 0.90,
           "fallback_policy": "deny"
       },
       "office_space": {
           "threshold": 0.75,
           "fallback_policy": "alert"
       },
       "common_areas": {
           "threshold": 0.65,
           "fallback_policy": "log"
       }
   }

Performance Factors
=================

Several factors can affect recognition performance:

- **Lighting conditions**: Consistent, well-distributed lighting improves accuracy
- **Image resolution**: Higher resolution improves recognition up to a point
- **Face angle**: Direct frontal faces provide best results
- **Distance from camera**: Performance degrades with distance
- **Occlusion**: Masks, sunglasses, and other face coverings reduce accuracy

.. note::
   The system is configured to adapt to minor variations in appearance such as facial hair changes, different hairstyles, or the addition/removal of glasses.

Handling Edge Cases
=================

The system includes special handling for common edge cases:

- **Twins/Look-alikes**: Additional verification can be enabled for individuals with similar facial features
- **Aging effects**: Periodic re-enrollment can account for natural aging changes
- **Temporary appearance changes**: Multiple reference images can be stored for individuals who frequently change appearance