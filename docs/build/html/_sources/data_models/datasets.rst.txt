========
Datasets
========

AI-Guard's recognition system is trained and evaluated using a combination of public and private datasets.

Public Datasets
=============

Labeled Faces in the Wild (LFW)
------------------------------

.. figure:: /_static/lfw-sample.png
   :alt: Labeled Faces in the Wild Sample Images
   :align: center
   
   Sample images from the LFW dataset

- **Size**: 13,233 images of 5,749 people
- **Purpose**: Benchmarking recognition performance
- **Characteristics**: Unconstrained face images with variations in pose, lighting, expression
- **Website**: http://vis-www.cs.umass.edu/lfw/

Face Detection Data Set and Benchmark (FDDB)
------------------------------------------

- **Size**: 2,845 images containing 5,171 faces
- **Purpose**: Training and evaluating face detection
- **Characteristics**: Diverse face appearances, complex backgrounds, occlusions
- **Website**: http://vis-www.cs.umass.edu/fddb/

CelebA
-----

- **Size**: 202,599 face images of 10,177 celebrities
- **Purpose**: Data augmentation and feature learning
- **Characteristics**: Large-scale dataset with attribute annotations
- **Website**: http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html

MS1M (Microsoft 1-Million Celebs)
-------------------------------

- **Size**: ~1M images of ~100K individuals
- **Purpose**: Pre-training face recognition models
- **Characteristics**: Large-scale dataset with high diversity
- **Citation**: Guo et al., "MS-Celeb-1M: A Dataset and Benchmark for Large-Scale Face Recognition"

Custom Datasets
=============

ENSAM Internal Dataset
--------------------

- **Size**: Controlled collection of enrolled users in the target environment
- **Purpose**: Fine-tuning for specific deployment environment
- **Characteristics**: 
  - Collected under actual operating conditions
  - Multiple angles per subject
  - Consistent capture protocol
  - Privacy consent obtained from all subjects

Environmental Variations Set
-------------------------

- **Purpose**: Testing robustness to environmental conditions
- **Characteristics**:
  - Various lighting conditions (daylight, fluorescent, low-light)
  - Different camera angles and distances
  - Captured at actual installation locations
  - Includes challenging cases (backlighting, shadows)

Dataset Usage
===========

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Dataset
     - Usage in AI-Guard
   * - LFW
     - Model evaluation and performance benchmarking
   * - FDDB
     - Training and fine-tuning detection models
   * - CelebA
     - Feature learning and data augmentation
   * - MS1M
     - Initial pre-training of recognition models
   * - ENSAM Internal
     - Domain-specific fine-tuning
   * - Environmental Variations
     - Robustness testing and threshold calibration

Data Collection Protocol
=====================

For custom datasets, the following protocol is followed:

1. **Informed Consent**: All subjects provide written consent
2. **Structured Capture**: Standardized distance, angles, and lighting
3. **Multiple Sessions**: Images captured over different days
4. **Quality Control**: Automatic quality assessment
5. **Metadata Tagging**: Images tagged with relevant conditions
6. **Privacy Protection**: Secure storage with access controls
7. **Retention Policy**: Clear data retention and deletion policies