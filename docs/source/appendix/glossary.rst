========
Glossary
========

Common terms and definitions used throughout the AI-GUARD system.

.. glossary::
   :sorted:

   Face Detection
      The process of identifying and localizing human faces in an image or video frame.

   Face Recognition
      The process of identifying or verifying a person's identity using their facial features.

   Face Embedding
      A mathematical representation of a face as a vector of floating-point numbers, typically 128 or 512 dimensions.

   Enrollment
      The process of registering a user's face in the system by capturing and storing their facial embeddings.

   Confidence Score
      A numerical value (typically between 0 and 1) indicating the system's confidence in a recognition match.

   False Positive
      When the system incorrectly identifies an unauthorized person as authorized.

   False Negative
      When the system fails to recognize an authorized person.

   Threshold
      A configurable confidence value used to determine whether a recognition score is considered a match.

   Cosine Similarity
      A mathematical measure used to compare face embeddings, calculating the cosine of the angle between two vectors.

   Euclidean Distance
      A mathematical measure of the straight-line distance between two points in Euclidean space, used as an alternative to cosine similarity.

   MTCNN
      Multi-task Cascaded Convolutional Networks, an algorithm used for face detection.

   FaceNet
      A deep learning model architecture used for generating face embeddings.

   ArcFace
      A loss function and model architecture designed to enhance face recognition accuracy by maximizing feature discrimination.

   Edge Device
      A device that processes data locally rather than in the cloud, such as a Jetson Nano or Raspberry Pi.

   Inference
      The process of running a trained machine learning model on new data to make predictions.