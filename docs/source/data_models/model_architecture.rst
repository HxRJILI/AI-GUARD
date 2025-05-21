==================
Model Architecture
==================

AI-Guard utilizes state-of-the-art deep learning architectures for face detection and recognition.

Face Detection
============

Multi-task Cascaded Convolutional Networks (MTCNN)
------------------------------------------------

The system uses MTCNN for face detection, which consists of three stages:

.. figure:: /_static/mtcnn-architecture.png
   :alt: MTCNN Architecture
   :align: center
   
   MTCNN's three-stage architecture

1. **Proposal Network (P-Net)**:
   - Generates candidate facial windows
   - Fast but with high false positive rate
   - Output: Bounding boxes and confidence scores

2. **Refinement Network (R-Net)**:
   - Filters false candidates from P-Net
   - More complex architecture for better discrimination
   - Output: Refined bounding boxes and confidence scores

3. **Output Network (O-Net)**:
   - Produces final facial landmarks
   - Highest complexity for precision
   - Output: Final bounding boxes, confidence scores, and 5 facial landmarks

.. code-block:: python
   :caption: P-Net Architecture

   def create_pnet(input_shape=(12, 12, 3)):
       input = Input(shape=input_shape)
       x = Conv2D(10, (3, 3), strides=1, padding='valid', name='conv1')(input)
       x = PReLU(shared_axes=[1, 2], name='prelu1')(x)
       x = MaxPooling2D(pool_size=2)(x)
       x = Conv2D(16, (3, 3), strides=1, padding='valid', name='conv2')(x)
       x = PReLU(shared_axes=[1, 2], name='prelu2')(x)
       x = Conv2D(32, (3, 3), strides=1, padding='valid', name='conv3')(x)
       x = PReLU(shared_axes=[1, 2], name='prelu3')(x)
       
       classifier = Conv2D(2, (1, 1), activation='softmax', name='classifier')(x)
       bbox_regressor = Conv2D(4, (1, 1), name='bbox_regressor')(x)
       
       model = Model([input], [classifier, bbox_regressor])
       return model

Alternative: RetinaFace
---------------------

For higher accuracy requirements, the system can use RetinaFace:

- Based on Feature Pyramid Network (FPN)
- Single-stage detector with multi-task learning
- Joint face detection and alignment
- Adds context attention module
- Higher computational requirements but better accuracy

Face Recognition
==============

FaceNet Architecture
------------------

For facial embeddings, the primary model is FaceNet with Inception-ResNet-v1 backbone:

.. figure:: /_static/facenet-architecture.png
   :alt: FaceNet Architecture
   :align: center
   
   FaceNet model architecture

- **Input**: 160×160 RGB images
- **Backbone**: Inception-ResNet-v1
- **Output**: L2-normalized 128-dimensional embedding vectors
- **Loss Function**: Triplet loss with semi-hard negative mining

The triplet loss function optimizes embeddings so that:
- Same identity faces have embeddings closer to each other
- Different identity faces have embeddings further apart

.. math::

   L = \sum_{i}^{N} \left[ \|f(x_i^a) - f(x_i^p)\|_2^2 - \|f(x_i^a) - f(x_i^n)\|_2^2 + \alpha \right]_+

Where:
- :math:`f(x_i^a)` is the anchor embedding
- :math:`f(x_i^p)` is the positive (same identity) embedding
- :math:`f(x_i^n)` is the negative (different identity) embedding
- :math:`\alpha` is the margin (typically 0.2)

Alternative: ArcFace
------------------

For even higher accuracy, the system can use ArcFace with ResNet-50 backbone:

- **Input**: 112×112 RGB images
- **Backbone**: ResNet-50
- **Output**: 512-dimensional embedding vectors
- **Loss Function**: Additive Angular Margin Loss (ArcFace)

ArcFace adds an angular margin to the target logit:

.. math::

   L = -\frac{1}{N} \sum_{i=1}^{N} \log \frac{e^{s \cos(\theta_{y_i} + m)}}{e^{s \cos(\theta_{y_i} + m)} + \sum_{j \neq y_i} e^{s \cos \theta_j}}

Where:
- :math:`\theta_{y_i}` is the angle between the feature and target weight
- :math:`m` is the additive angular margin (0.5 radians)
- :math:`s` is the feature scale (64)

Model Comparison
==============

.. list-table::
   :header-rows: 1
   :widths: 20 25 25 30

   * - Model
     - Embedding Size
     - LFW Accuracy
     - Computational Requirements
   * - FaceNet
     - 128-D
     - 99.63%
     - Medium
   * - ArcFace
     - 512-D
     - 99.82%
     - High
   * - CosFace
     - 512-D
     - 99.73%
     - Medium-High
   * - SphereFace
     - 512-D
     - 99.42%
     - Medium

Implementation Details
===================

The models are implemented using TensorFlow with the following considerations:

- **Quantization**: INT8 quantization for edge deployment
- **Pruning**: Sparse model pruning for size reduction
- **Batch Normalization**: Folded into convolutional layers for inference
- **Memory Optimization**: Model-specific optimizations to reduce memory footprint