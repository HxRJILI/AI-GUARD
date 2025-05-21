=================
Deployment Options
=================

AI-GUARD supports multiple deployment configurations to fit different organizational needs.

Local Deployment
===============

Suitable for testing, development, or small-scale implementations.

.. code-block:: bash
   :caption: Local deployment with Docker Compose

   # Clone the repository
   git clone https://github.com/ensam/ai-guard.git
   cd ai-guard

   # Set up environment variables
   cp .env.example .env
   # Edit .env with your configuration

   # Start the services
   docker-compose up -d

Cloud Deployment
==============

For production environments with high availability and scalability requirements.

Kubernetes Deployment
-------------------

.. code-block:: bash
   :caption: Kubernetes deployment with Helm

   # Add the AI-GUARD Helm repository
   helm repo add ai-guard https://charts.ai-guard.io
   helm repo update

   # Create namespace
   kubectl create namespace ai-guard

   # Install PostgreSQL dependency
   helm install postgres bitnami/postgresql -n ai-guard \
     --set auth.postgresPassword=secretpassword \
     --set persistence.size=10Gi

   # Install AI-GUARD
   helm install ai-guard ai-guard/ai-guard -n ai-guard \
     --set global.postgresql.auth.password=secretpassword \
     --set ingress.enabled=true \
     --set ingress.hosts[0].host=ai-guard.example.com

Edge Device Deployment
====================

For locations where recognition processing needs to happen locally.

Jetson Nano Setup
---------------

.. code-block:: bash
   :caption: Jetson Nano installation

   # Flash the Jetson Nano with JetPack 4.6
   # Follow NVIDIA's instructions at https://developer.nvidia.com/jetpack-sdk

   # Clone the edge device repository
   git clone https://github.com/ensam/ai-guard-edge.git
   cd ai-guard-edge

   # Install dependencies
   ./install_dependencies.sh

   # Configure the device
   python configure.py --device-id="entry-east-1" --server-url="https://ai-guard.example.com"

   # Start the services
   sudo systemctl enable ai-guard
   sudo systemctl start ai-guard