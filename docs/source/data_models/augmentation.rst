============
Augmentation
============

To improve model robustness, various data augmentation techniques are applied during training.

Geometric Transformations
=======================

.. figure:: /_static/geometric-augmentations.png
   :alt: Geometric Augmentation Examples
   :align: center
   
   Examples of geometric transformations applied to face images

The following geometric transformations are applied:

Random Rotation
-------------

- **Range**: ±15 degrees
- **Purpose**: Simulate head tilt variations
- **Implementation**: 

.. code-block:: python

   def random_rotation(image, max_angle=15):
       angle = random.uniform(-max_angle, max_angle)
       return rotate(image, angle, reshape=False)

Random Horizontal Flipping
------------------------

- **Probability**: 50%
- **Purpose**: Increase dataset size and reduce bias
- **Implementation**:

.. code-block:: python

   def random_flip(image):
       if random.random() > 0.5:
           return np.fliplr(image)
       return image

Random Cropping
-------------

- **Range**: 90-100% of original size
- **Purpose**: Simulate alignment variations
- **Constraint**: Must maintain facial region

Perspective Transformations
-------------------------

- **Type**: Slight affine and perspective warps
- **Purpose**: Simulate different camera angles
- **Implementation**: Uses OpenCV's `getPerspectiveTransform`

Photometric Transformations
=========================

Brightness Variation
------------------

- **Range**: ±25% adjustment
- **Purpose**: Simulate different lighting intensities
- **Implementation**:

.. code-block:: python

   def random_brightness(image, max_delta=0.25):
       delta = random.uniform(-max_delta, max_delta)
       return np.clip(image + delta, 0.0, 1.0)

Contrast Adjustment
-----------------

- **Range**: ±20% adjustment
- **Purpose**: Simulate different camera settings
- **Implementation**:

.. code-block:: python

   def random_contrast(image, lower=0.8, upper=1.2):
       factor = random.uniform(lower, upper)
       mean = np.mean(image, axis=(0, 1), keepdims=True)
       return np.clip((image - mean) * factor + mean, 0.0, 1.0)

Gaussian Noise
------------

- **Sigma**: 0.01-0.03
- **Purpose**: Improve robustness to sensor noise
- **Implementation**: Add random Gaussian noise to pixel values

Color Jittering
-------------

- **Hue**: ±10 degrees
- **Saturation**: ±20%
- **Purpose**: Account for different color balances

Occlusion Simulation
==================

Random Rectangular Occlusions
---------------------------

- **Size**: 10-25% of face area
- **Count**: 1-2 rectangles
- **Purpose**: Simulate partial face coverage

Face Mask Simulation
------------------

- **Types**: Medical, cloth, N95 masks
- **Purpose**: Explicitly handle common real-world occlusions
- **Implementation**: Alpha blending of mask templates

Glasses Simulation
----------------

- **Types**: Various eyeglasses and sunglasses
- **Purpose**: Account for eyewear variations
- **Implementation**: Alpha blending with eyeglass templates

Augmentation Pipeline
===================

During training, augmentations are applied in a configurable pipeline:

.. code-block:: python

   def augment_batch(batch, config):
       augmented = []
       for image in batch:
           # Always apply basic standardization
           image = standardize(image)
           
           # Apply geometric transforms with probability
           if random.random() < config["geo_prob"]:
               image = apply_geometric_transforms(image, config)
           
           # Apply photometric transforms with probability
           if random.random() < config["photo_prob"]:
               image = apply_photometric_transforms(image, config)
           
           # Apply occlusion with lower probability
           if random.random() < config["occlude_prob"]:
               image = apply_occlusions(image, config)
               
           augmented.append(image)
       return np.array(augmented)

Implementation Details
====================

The augmentation pipeline is implemented using:

- **TensorFlow's image operations**: For GPU-accelerated augmentation during training
- **imgaug library**: For complex augmentations during dataset preparation
- **Albumentations**: For high-performance transformation pipelines

Augmentations are applied with varying probabilities to ensure the model sees a diverse range of variations while still training primarily on realistic images.