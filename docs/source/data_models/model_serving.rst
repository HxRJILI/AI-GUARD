=============
Model Serving
=============

AI-Guard utilizes a robust infrastructure for serving machine learning models in production.

Serving Infrastructure
====================

TensorFlow Serving
----------------

The system primarily uses TensorFlow Serving for reliable, high-performance model deployment:

.. figure:: /_static/tf-serving-architecture.png
   :alt: TensorFlow Serving Architecture
   :align: center
   
   TensorFlow Serving deployment architecture

Key benefits:
- RESTful and gRPC API endpoints
- Model versioning with automatic loading
- Request batching for efficiency
- Multi-model serving
- GPU acceleration support

.. code-block:: bash
   :caption: TensorFlow Serving deployment

   # Pull TensorFlow Serving Docker image
   docker pull tensorflow/serving:latest
   
   # Start TensorFlow Serving container
   docker run -p 8501:8501 -p 8500:8500 \
     --name tf_serving \
     --mount type=bind,source=/path/to/models/facenet,target=/models/facenet \
     --mount type=bind,source=/path/to/models/mtcnn,target=/models/mtcnn \
     -e MODEL_NAME=facenet -e MODEL_BASE_PATH=/models/facenet \
     -t tensorflow/serving

TorchServe (Alternative)
----------------------

For PyTorch-based models, the system can use TorchServe:

- Similar features to TensorFlow Serving
- Custom handlers for preprocessing and postprocessing
- Model management API
- Metrics API for monitoring

API Endpoints
===========

Detection Endpoint
----------------

.. http:post:: /v1/models/face_detection:predict

   Detects faces in an image and returns bounding boxes.

   **Example request**:

   .. sourcecode:: http

      POST /v1/models/face_detection:predict HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "signature_name": "serving_default",
        "instances": [
          {
            "b64": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0a..."
          }
        ]
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "predictions": [
          {
            "detection_boxes": [
              [0.15, 0.25, 0.52, 0.62],
              [0.55, 0.35, 0.92, 0.72]
            ],
            "detection_scores": [0.992, 0.851],
            "landmark_positions": [
              [[0.34, 0.38], [0.41, 0.38], [0.37, 0.45], [0.33, 0.51], [0.41, 0.51]],
              [[0.74, 0.48], [0.81, 0.48], [0.77, 0.55], [0.73, 0.61], [0.81, 0.61]]
            ]
          }
        ]
      }

Recognition Endpoint
------------------

.. http:post:: /v1/models/face_recognition:predict

   Generates face embeddings for aligned face images.

   **Example request**:

   .. sourcecode:: http

      POST /v1/models/face_recognition:predict HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "signature_name": "serving_default",
        "instances": [
          {
            "aligned_face": {
              "b64": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRof..."
            }
          }
        ]
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "predictions": [
          {
            "embedding": [-0.024, 0.105, -0.178, 0.039, ..., 0.071],
            "embedding_norm": 1.0
          }
        ]
      }

End-to-End Endpoint
-----------------

.. http:post:: /v1/models/face_pipeline:predict

   Performs detection, alignment, and recognition in a single request.

   **Example request**:

   .. sourcecode:: http

      POST /v1/models/face_pipeline:predict HTTP/1.1
      Host: api.aiguard.example.com
      Content-Type: application/json

      {
        "signature_name": "serving_default",
        "instances": [
          {
            "image": {
              "b64": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRof..."
            }
          }
        ]
      }

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "predictions": [
          {
            "faces": [
              {
                "bbox": [0.15, 0.25, 0.52, 0.62],
                "confidence": 0.992,
                "landmarks": [[0.34, 0.38], [0.41, 0.38], [0.37, 0.45], [0.33, 0.51], [0.41, 0.51]],
                "embedding": [-0.024, 0.105, -0.178, 0.039, ..., 0.071]
              },
              {
                "bbox": [0.55, 0.35, 0.92, 0.72],
                "confidence": 0.851,
                "landmarks": [[0.74, 0.48], [0.81, 0.48], [0.77, 0.55], [0.73, 0.61], [0.81, 0.61]],
                "embedding": [0.102, -0.056, 0.221, -0.173, ..., 0.045]
              }
            ]
          }
        ]
      }

Serving Optimization
==================

Request Batching
--------------

The system optimizes throughput through batched processing:

- Dynamic batching of incoming requests
- Configurable batch size and timeout
- Automatic batch processing on GPU
- Prioritization for real-time requests

.. code-block:: json
   :caption: Batching configuration

   {
     "max_batch_size": 32,
     "batch_timeout_micros": 5000,
     "max_enqueued_batches": 100,
     "num_batch_threads": 8
   }

Model Optimization
----------------

Models are optimized for inference performance:

- TensorRT integration for GPU acceleration
- Quantization to INT8 or FP16 precision
- Operator fusion for computation efficiency
- CPU-specific optimizations (AVX, SSE)
- Memory footprint reduction

Scaling and High Availability
===========================

The model serving infrastructure is designed for high availability:

- Kubernetes-based horizontal scaling
- Load balancing across serving instances
- Health monitoring and automatic restarts
- Graceful version updates
- Regional failover support
- Auto-scaling based on request load

Monitoring
=========

Comprehensive metrics are collected for performance monitoring:

- Request latency (p50, p95, p99)
- Throughput (requests per second)
- Model loading time
- Batch size distribution
- GPU/CPU utilization
- Memory usage
- Error rates
- Cache hit rates