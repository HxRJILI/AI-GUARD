=============
Local Setup
=============

Guide for setting up a local development environment for AI-GUARD.

Prerequisites
===========

- Git
- Docker and Docker Compose
- Node.js 18.x or newer
- Python 3.10 or newer
- PostgreSQL 14.x (for native development)
- NVIDIA GPU with CUDA support (optional, for ML development)

Clone the Repository
==================

.. code-block:: bash

   git clone https://github.com/ensam/ai-guard.git
   cd ai-guard

Development Environment
=====================

The simplest way to get started is using Docker Compose:

.. code-block:: bash

   # Create a local environment file
   cp .env.example .env.dev

   # Edit the environment file with your configuration
   nano .env.dev

   # Start the development environment
   docker-compose -f docker-compose.dev.yml up -d

   # The API will be available at http://localhost:8000
   # The frontend will be available at http://localhost:3000

Frontend Development
==================

.. code-block:: bash

   # Navigate to the frontend directory
   cd frontend

   # Install dependencies
   npm install

   # Start the development server
   npm run dev

Backend Development
=================

.. code-block:: bash

   # Navigate to the backend directory
   cd backend

   # Create a virtual environment
   python -m venv venv
   source venv/bin/activate  # On Windows, use: venv\Scripts\activate

   # Install dependencies
   pip install -r requirements.txt
   pip install -r requirements-dev.txt

   # Run migrations
   python manage.py migrate

   # Start the development server
   python manage.py runserver

ML Model Development
==================

.. code-block:: bash

   # Navigate to the ml directory
   cd ml

   # Create a virtual environment
   python -m venv venv
   source venv/bin/activate  # On Windows, use: venv\Scripts\activate

   # Install dependencies
   pip install -r requirements.txt

   # Run the model development notebook
   jupyter notebook