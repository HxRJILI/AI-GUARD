# AI-Guard 🛡️

**AI-Guard** is an intelligent, real-time facial recognition security system developed for ENSAM Meknès. It aims to prevent unauthorized access by identifying individuals at campus entry points using deep learning and computer vision.

<p align="center">
  <img src="https://img.shields.io/badge/Status-Proof%20of%20Concept-yellow" alt="Status: Proof of Concept">
  <img src="https://img.shields.io/badge/Python-3.8%2B-blue" alt="Python 3.8+">
  <img src="https://img.shields.io/badge/License-MIT-green" alt="License: MIT">
</p>

## 📋 Table of Contents
- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Dataset Approach](#dataset-approach)
- [Project Structure](#project-structure)
- [Setup Instructions](#setup-instructions)
- [Face Enrollment & Recognition Flow](#face-enrollment--recognition-flow)
- [Model Evaluation](#model-evaluation)
- [Technical Approach](#technical-approach)
- [Future Security Implementation](#future-security-implementation)
- [Key Notebooks](#key-notebooks)
- [Authors](#authors)
- [Contact](#contact)

## 🔍 Overview

AI-Guard is designed to enhance security at ENSAM Meknès by using facial recognition to distinguish between authorized and unauthorized individuals. The system captures and analyzes faces at entry points, automatically alerting security personnel when an unrecognized person is detected.

## 🚀 Features

- 🎥 **Face Enrollment System**  
  Multi-angle face capture with automatic quality validation.

- 🔍 **Real-Time Recognition**  
  Continuous monitoring and recognition using deep learning models.

- 🔔 **Automated Alerts**  
  Notifies security personnel of any unauthorized access attempts.

- 🧠 **Fine-Tuned Recognition Model**  
  Built on top of FaceNet/ArcFace, fine-tuned on custom datasets for high accuracy.

- 📊 **Comprehensive Logging**  
  Records access attempts and security events for later review.

- 📁 **Secure Design**  
  Face embeddings stored with privacy and security in mind.

## 🧱 Tech Stack

| Component         | Technology                |
|------------------|---------------------------|
| Face Detection   | MTCNN / Faster R-CNN      |
| Face Recognition | FaceNet / ArcFace / ResNet18 |
| Dataset Processing | Python (OpenCV, Numpy)  |
| Model Training   | PyTorch                   |
| Face Enrollment  | Custom webcam script      |

## 📊 Dataset Approach

Our face recognition system uses several approaches to ensure robust performance:

1. **Enhanced LFW Dataset** - We use a heavily processed version of the Labeled Faces in the Wild dataset as our foundation. Beyond simple augmentation, we applied extensive preprocessing, transformation, and enhancement techniques to significantly improve diversity, quality, and overall model training effectiveness. This extensively manipulated dataset is available on Kaggle:
   ```python
   import kagglehub
   # Download latest version
   path = kagglehub.dataset_download("wiameelhafid/aiguard-split-data")
   print("Path to dataset files:", path)
   ```

2. **Custom Face Registration** - We developed a script (`NNNNNNNNN1111.ipynb`) that enables new users to register their faces by capturing three angles:
   - Front-facing view
   - Right profile view
   - Left profile view

3. **Proof of Concept Dataset** - Due to limited access to actual ENSAM student/staff data, we created a demonstration dataset using:
   - Well-known public figures
   - Three-angle captures for each individual
   - Validation through video recognition tests

This multi-angle approach improves recognition accuracy across different viewing angles and lighting conditions.

## 🛠️ Project Structure

```bash
ai-guard/
├── notebooks/          # Jupyter notebooks for model development
│   ├── NNNNNNNNN1111.ipynb  # Face registration script
│   └── *.ipynb         # Model training, testing, and evaluation notebooks
├── models/             # Trained model files
├── utils/              # Helper functions
│   ├── face_detection.py
│   ├── data_augmentation.py
│   └── model_training.py
├── datasets/           # Dataset management
│   ├── lfw_processed/  # Processed LFW dataset
│   └── custom/         # Custom face registrations
└── README.md
```

## ⚙️ Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/ai-guard.git
cd ai-guard
```

### 2. Environment Setup

```bash
# Create a virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 3. Face Registration

To register a new face in the system:

```bash
# Open the registration notebook
jupyter notebook notebooks/NNNNNNNNN1111.ipynb

# Follow the instructions in the notebook to:
# 1. Capture front, left, and right profile views
# 2. Process and save the face embeddings
```

### 4. Running the Model

```bash
# Open one of the model testing notebooks
jupyter notebook notebooks/model_testing.ipynb

# Follow the instructions to test recognition on new images or video
```

## 🧪 Face Enrollment & Recognition Flow

1. **Face Detection**: Using MTCNN to locate faces in the input image.
2. **Multi-Angle Capture**: Registration requires front, left, and right profile views.
3. **Feature Extraction**: Images are processed through our model (FaceNet/ArcFace) to generate embeddings.
4. **Embedding Storage**: Face embeddings (feature vectors) are saved to be used as reference.
5. **Recognition Process**: 
   - New images are processed to extract face embeddings
   - Cosine similarity is calculated between the new embedding and stored embeddings
   - Threshold-based decision to determine if the face is recognized

## 📊 Model Evaluation

We evaluated several models to find the optimal approach:

| Model | Recognition Accuracy | Speed | Performance in Variable Lighting |
|-------|---------------------|-------|--------------------------------|
| ResNet18 | Very Good | Fast | Good |
| MTCNN + FaceNet | Excellent | Moderate | Very Good |
| ArcFace | Excellent | Moderate | Excellent |
| Faster R-CNN | Good | Slow | Very Good |

Our tests with the augmented LFW dataset and custom proof-of-concept dataset showed that the combination of MTCNN for detection and FaceNet/ArcFace for recognition provides the best balance of accuracy and performance.

## 🧠 Technical Approach

1. **Data Augmentation**: We enhanced the LFW dataset through rotation, cropping, brightness adjustments, and other techniques to improve model robustness.

2. **Multi-Angle Registration**: Our system captures three different angles of each face to improve recognition accuracy across viewpoints.

3. **Model Comparison**: We systematically evaluated multiple models to find the best accuracy-performance trade-off for our specific use case.

4. **Proof of Concept Testing**: Using public figures as test subjects, we demonstrated the system's ability to recognize faces from videos after training on still images.

## 🛡️ Future Security Implementation

For the full production system at ENSAM, we plan to implement:

- Encrypted storage of face embeddings
- Strict user consent protocols
- Access controls for security personnel
- Secure data handling practices
- Regular model retraining with new data

## 🔬 Key Notebooks

- `NNNNNNNNN1111.ipynb`: Face registration workflow with webcam integration
- Model training notebooks with various architectures
- Evaluation notebooks comparing model performance
- Data augmentation scripts to enhance the LFW dataset

## 👥 Authors

- Wiame El Hafid
- Houssam Rjili

## 📬 Contact

Have questions about the project? 
Reach out to the authors or open an issue in this repository.
